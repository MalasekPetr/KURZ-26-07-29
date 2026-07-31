# Tahák · SPO API triky: ID, definice seznamů a interní názvy

Praktické dotazy, které při automatizaci SharePointu potřebujete pořád dokola.
Tři cesty k témuž: **Graph** (URL do Graph Exploreru nebo `Invoke-MgGraphRequest`),
**PnP PowerShell** (cmdlety) a **SharePoint REST** (`/_api/...` — funguje i v adresním
řádku prohlížeče, když jste přihlášení na webu).

## ID objektů — site, web, seznam

Skoro každé API volání chce nějaké ID. Jak je zjistit:

```http
# Graph: site ID z URL webu (hostname + server-relativní cesta)
GET https://graph.microsoft.com/v1.0/sites/<tenant>.sharepoint.com:/sites/<nazev-webu>
# → "id": "<hostname>,<siteCollectionId>,<webId>" (trojice — Graph ji používá celou)

# Kořenový web tenantu
GET https://graph.microsoft.com/v1.0/sites/root
```

```powershell
# PnP (po Connect-PnPOnline na daný web)
Get-PnPSite -Includes Id | Select-Object Id          # site collection ID
Get-PnPWeb | Select-Object Id, Title                  # web ID
Get-PnPList | Select-Object Id, Title                 # ID všech seznamů
```

```http
# SharePoint REST (i v prohlížeči)
https://<tenant>.sharepoint.com/sites/<web>/_api/site/id
https://<tenant>.sharepoint.com/sites/<web>/_api/web/id
```

## Definice seznamu / knihovny

Co to vlastně je za seznam — šablona, skrytost, sloupce, obsahové typy:

```http
# Graph: seznamy webu včetně "list" facetu (template, hidden)
GET /sites/{site-id}/lists?$select=id,displayName,list

# Kompletní definice jednoho seznamu
GET /sites/{site-id}/lists/{list-id}?$expand=columns,contentTypes
```

```powershell
# PnP: základ + plná definice polí
Get-PnPList -Identity "Dokumenty"
Get-PnPList -Identity "Dokumenty" -Includes Fields, ContentTypes
```

Tip: skryté systémové seznamy odfiltrujete přes `Get-PnPList | Where-Object Hidden -eq $false`.

## Interní názvy polí (InternalName)

Zobrazovaný název (`Title`) je pro lidi a **dá se kdykoli přejmenovat**; interní název
(`InternalName`) vzniká při založení pole a **už se nikdy nemění** — a právě jeho chtějí
CAML dotazy, REST filtry i Graph `fieldValueSet`. Záludnost: mezera v názvu při
založení → `_x0020_` v interním názvu („Datum schválení" → `Datum_x0020_schv_x00e1_len_x00ed_`
— proto zakládejte pole bez diakritiky a mezer, přejmenovat displayName můžete potom).

```powershell
# PnP: mapa display → internal pro daný seznam
Get-PnPField -List "Dokumenty" |
  Where-Object Hidden -eq $false |
  Select-Object Title, InternalName, TypeAsString | Sort-Object Title
```

```http
# REST ekvivalent
/_api/web/lists/getbytitle('Dokumenty')/fields?$select=Title,InternalName,TypeAsString&$filter=Hidden eq false
```

## Choice pole — povolené hodnoty

Než začnete dávkově zapisovat, zjistěte, co pole vůbec přijme:

```powershell
# PnP
(Get-PnPField -List "Dokumenty" -Identity "Status").Choices
```

```http
# Graph: v definici sloupce
GET /sites/{site-id}/lists/{list-id}/columns
# → hledejte "choice": { "choices": ["Návrh", "Ke schválení", "Schváleno"] }

# REST
/_api/web/lists/getbytitle('Dokumenty')/fields?$filter=TypeAsString eq 'Choice'
```

## Indexy a velikost seznamu

Před každým dotazem nad neznámým seznamem dvě otázky: kolik toho tam je a podle čeho
můžu filtrovat bez pádu na threshold 5000.

```powershell
# Kolik položek a kdy naposledy změna
Get-PnPList -Identity "Dokumenty" | Select-Object Title, ItemCount, LastItemUserModifiedDate

# Které sloupce jsou indexované (= podle čeho můžu bezpečně filtrovat)
Get-PnPField -List "Dokumenty" |
  Where-Object Indexed -eq $true |
  Select-Object Title, InternalName, TypeAsString

# Přidat index (limit ~20 na seznam; zavést dokud je seznam malý)
Set-PnPField -List "Dokumenty" -Identity "Stav" -Values @{ Indexed = $true }
```

Souvislosti, limity a checklist pro práci s velkými seznamy:
[`explainer-large-lists.md`](explainer-large-lists.md).

## Rychlé kombinace do praxe

```powershell
# Přehled webu na jeden pohled: co tu je a jak je to velké
Get-PnPList | Where-Object Hidden -eq $false |
  Select-Object Title, ItemCount, LastItemUserModifiedDate |
  Sort-Object ItemCount -Descending
```

- V Graph Exploreru si dotaz vyzkoušejte dřív, než ho dáte do skriptu — chybu v `$filter`
  vrátí hned a čitelně.
- `$select` používejte všude — méně dat, rychlejší odpověď, čitelnější JSON.
- Tenhle tahák + priming prompt ([`../../day-1/formats-fundamentals/copilot-priming-prompt.md`](../../day-1/formats-fundamentals/copilot-priming-prompt.md))
  = Copilot Chat generuje skripty nad správnými názvy polí; internal names mu vždy
  dodejte v promptu, sám je nezná.

## Zdroje (Microsoft)

- [Working with SharePoint sites in Microsoft Graph](https://learn.microsoft.com/en-us/graph/api/resources/sharepoint)
- [listInfo / columnDefinition resource types (Graph)](https://learn.microsoft.com/en-us/graph/api/resources/columndefinition)
- [Working with lists and list items with REST](https://learn.microsoft.com/en-us/sharepoint/dev/sp-add-ins/working-with-lists-and-list-items-with-rest)
- [PnP PowerShell — Get-PnPField](https://pnp.github.io/powershell/cmdlets/Get-PnPField.html)
