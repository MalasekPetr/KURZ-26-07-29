# Explainer · Velké seznamy: threshold 5000, indexy a throttling ve velkém

Skript, který funguje na testovacím seznamu se 100 položkami, umí spadnout na produkčním
s 5001. Tenhle explainer je o tom, proč — a co s tím. Dvě samostatné věci, které se
pletou: **list view threshold** (limit jednoho dotazu) a **throttling** (limit rychlosti
volání).

## 1. List view threshold = 5000

**Není to strop velikosti seznamu.** SPO seznam může mít miliony položek. 5000 je limit
na to, kolik položek smí **jeden dotaz projít**, než ho server odmítne — chrání sdílenou
databázi před uzamčením řádků kvůli jednomu nešikovnému dotazu.

Typická chybová hláška: *„The attempted operation is prohibited because it exceeds the
list view threshold."*

Co ho spouští:

- zobrazení nebo dotaz **bez filtru na indexovaném sloupci**,
- **řazení** podle neindexovaného sloupce,
- seskupení, souhrny, filtr na vícehodnotovém sloupci,
- načtení „všech položek" bez stránkování.

## 2. Indexované sloupce — nástroj, jak dotaz zúžit

Index je pomocná vyhledávací struktura nad sloupcem. Se filtrem na indexovaném sloupci
server **nejdřív zúží množinu** a threshold už nepřekročí.

```powershell
# Které sloupce jsou indexované?
Get-PnPField -List "Dokumenty" |
  Where-Object Indexed -eq $true |
  Select-Object Title, InternalName, TypeAsString

# Přidat index na existující sloupec
Set-PnPField -List "Dokumenty" -Identity "Stav" -Values @{ Indexed = $true }
```

V UI: *Nastavení seznamu → Indexované sloupce* (tam se dělá i **složený index** —
primární + sekundární sloupec pro dvojici filtrů, která se používá pořád).

Co je dobré vědět předem:

- **Maximum ~20 indexů na seznam** — index není zdarma, zpomaluje zápis; indexujte to,
  podle čeho reálně filtrujete, ne všechno.
- **Indexovat nelze** vícehodnotové sloupce (multi-choice, multi-person, multi managed
  metadata), počítané sloupce a víceřádkový text.
- **Index zaveďte, dokud je seznam malý.** Na seznamu, který už threshold překročil,
  je zavedení indexu problematické (SPO si dnes většinu indexů zakládá sám —
  *automatic index management* — ale spoléhat se na to u vlastního návrhu je hazard).
- Vlastní šablona seznamu je nejlepší místo, kde index nastavit hned při vzniku
  ([`../site-list-templates/`](../site-list-templates/)).

## 3. Jak psát dotazy, aby threshold nebolel

**Anti-pattern** — stáhne celý seznam na klienta a filtruje až doma; na velkém seznamu
skončí chybou nebo minutami čekání:

```powershell
Get-PnPListItem -List "Dokumenty" | Where-Object { $_.FieldValues.Stav -eq "Nová" }
```

**Správně** — nechat filtrovat a stránkovat server:

```powershell
# a) PnP se stránkováním: PnP načítá po dávkách, threshold nepřekročí
Get-PnPListItem -List "Dokumenty" -PageSize 500

# b) CAML dotaz s filtrem na INDEXOVANÉM sloupci + RowLimit
$caml = @"
<View><Query>
  <Where><Eq><FieldRef Name='Stav'/><Value Type='Text'>Nová</Value></Eq></Where>
</Query><RowLimit>500</RowLimit></View>
"@
Get-PnPListItem -List "Dokumenty" -Query $caml

# c) Graph: filtr na serveru + průchod stránkami (Lab 4)
#    /sites/{id}/lists/{id}/items?$expand=fields&$filter=fields/Stav eq 'Nová'&$top=100
```

Pravidlo do praxe: **filtruj na serveru, ber po stránkách, na velký seznam nikdy
nesahej „celý".**

## 4. Throttling ve velkém — nad rámec Graph 429

Základ (429, závazné `Retry-After`, transientní vs permanentní chyby) je v
[`README.md`](README.md). Co k tomu patří u objemných operací proti SharePointu:

- **SPO throttluje per uživatele i per aplikaci** a vrací `429` nebo `503`
  s `Retry-After`. Chová se to jako pojistka, ne porucha.
- **Nehádejte to paralelizací.** Deset paralelních vláken vás throttlingu nezbaví —
  přivolají ho. Rychlejší je jedna sekvenční dávka než pět zablokovaných vláken.
- **Dávkové API místo N volání**: `New-PnPBatch` / `Invoke-PnPBatch` u zápisů,
  `$batch` v Graphu (max 20 requestů) — méně volání = méně throttlingu.
- **Dekorace user agenta**: Microsoft u vlastních volání do SPO/CSOM žádá identifikaci
  aplikace ve tvaru `NONISV|Firma|NazevAplikace/1.0` — nedekorovaný provoz je
  throttlován agresivněji. PnP.PowerShell si vlastní dekorovaný user agent nastavuje
  sám; pokud si voláte REST přímo (`Invoke-RestMethod`), dekorujte:

  ```powershell
  Invoke-RestMethod -Uri $url -Headers $h -UserAgent "NONISV|KUZK|InventuraWebu/1.0"
  ```

- **`RateLimit-*` hlavičky** (`RateLimit-Limit`, `-Remaining`, `-Reset`) umožňují zpomalit
  **předtím**, než přijde 429 — u dlouhých dávek se vyplatí je čít a zvolnit.
- Dlouhé úlohy pouštějte mimo špičku a se **checkpointem** (kde jsem skončil), aby se
  po přerušení nezačínalo od nuly.

## 5. Checklist před spuštěním skriptu na velký seznam

- [ ] Vím, kolik položek seznam má (`(Get-PnPList "Dokumenty").ItemCount`).
- [ ] Filtruju na serveru, ne `Where-Object` po stažení všeho.
- [ ] Sloupce, podle kterých filtruju/řadím, jsou **indexované**.
- [ ] Čtu po stránkách (`-PageSize`, `RowLimit`, `nextLink`).
- [ ] Zápisy jdou dávkově (`Invoke-PnPBatch` / `$batch`).
- [ ] Retry respektuje `Retry-After`; nepouštím paralelní vlákna „pro rychlost".
- [ ] U dlouhé úlohy mám log a checkpoint, ať vím, kde pokračovat.

## Zdroje (Microsoft)

- [Manage large lists and libraries](https://learn.microsoft.com/en-us/sharepoint/manage-large-lists)
- [Add an index to a list column](https://support.microsoft.com/en-us/office/add-an-index-to-a-sharepoint-column-f3f00554-b7dc-44d1-a2ed-d477eac463b0)
- [Avoid getting throttled or blocked in SharePoint Online](https://learn.microsoft.com/en-us/sharepoint/dev/general-development/how-to-avoid-getting-throttled-or-blocked-in-sharepoint-online)
- [Microsoft Graph throttling guidance](https://learn.microsoft.com/en-us/graph/throttling)

## Stav produktu / delta
> [!WARNING]
> Ověřit k datu běhu — hodnoty limitů (5000, ~20 indexů, 20 requestů v `$batch`),
> chování *automatic index management* a dostupnost `RateLimit-*` hlaviček se mění.
> Threshold ani počet indexů nejsou nastavitelné v SPO (na rozdíl od on-premises).
