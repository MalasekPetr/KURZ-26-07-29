# Microsoft Graph prakticky

> Typ: povinný · Den: 3 · Odhad: 45 min výklad + 60 min Lab 4

## Cíle
- Složit vlastní Graph dotaz: endpoint, `$select`, `$filter`, `$top` — nejdřív
  v Graph Exploreru, pak ve skriptu.
- Korektně projít stránkovanou odpověď (`@odata.nextLink`) — žádná tichá ztráta dat
  po první stránce.
- Základně rozlišit chyby: co retryovat (429, 5xx) a co ne (ostatní 4xx) — a vědět, že
  `Retry-After` je závazný pokyn, ne doporučení.
- Výhled: vědět, že existuje batching, delta query a throttling limity — kam sáhnout,
  až skripty porostou.

## Výklad

### Anatomie dotazu — od Exploreru ke skriptu
Graph Explorer (`https://developer.microsoft.com/graph/graph-explorer`) je sandbox, kde
se dotaz poskládá bez jediného řádku kódu — návrat ke cvičení z D1
([`../../day-1/api-landscape/exercise-graph-explorer.md`](../../day-1/api-landscape/exercise-graph-explorer.md)),
teď se složitějšími dotazy a přenosem do skriptu. Anatomie:

```http
GET https://graph.microsoft.com/v1.0/users?$select=displayName,mail&$filter=accountEnabled eq true&$top=10
```

- **endpoint** (`/users`, `/sites`, `/me/messages`) — co chci;
- **`$select`** — jen sloupce, které potřebuji (rychlejší, čitelnější);
- **`$filter`** — podmínka na straně serveru (ne stahovat všechno a filtrovat doma);
- **`$top`** — velikost stránky.

Tentýž dotaz pak ve skriptu: `Invoke-MgGraphRequest -Uri "..."` (nebo přes
`Connect-CourseTarget` wrapper z Labu 3). Odpověď je JSON — od D1 domácí formát.

### Stránkování — první dospělý problém každého skriptu
Graph nikdy nevrací "všechno" — vrací stránku a odkaz na další v `@odata.nextLink`.
Skript, který nextLink nesleduje, **tiše** zpracuje jen první stránku a tváří se
úspěšně — nejzáludnější kategorie chyby: žádná chybová hláška, jen špatná data.
Vzor: smyčka `while ($response.'@odata.nextLink')`.

### Chyby: co retryovat a co ne
Chybový objekt Graphu má strojově čitelné `code` — vázat logiku na něj, ne na text
`message`. Pravidlo do začátku: **429** → počkat přesně `Retry-After` sekund (závazné,
ne orientační — předčasný retry throttling prodlužuje) a zkusit znovu; **5xx** → retry
s rostoucím čekáním; **ostatní 4xx** (403, 404) → neretryovat, to je chyba k vyřešení,
ne k opakování. Graph SDK mají retry vestavěný — vlastní smyčku psát jen tam, kde SDK
nepomáhá.

```mermaid
flowchart TD
  A[Request] --> B{Status kód}
  B -->|200/201| C[OK]
  B -->|429| D[Počkat Retry-After, pak retry]
  B -->|5xx| E[Exponenciální backoff, retry]
  B -->|jiné 4xx| F[Neretryovat, logovat jako chybu]
```

### Výhled — až skripty porostou
- **JSON batching** (`POST /$batch`): až 20 requestů v jednom volání; pozor, batch-level
  200 neznamená úspěch všech dílčích odpovědí.
- **Delta query**: "co se změnilo od minula" místo plného re-scanu.
- **Throttling limity** se liší po workloadu a mění se v čase.

V tomto kurzu jen orientačně — plná hloubka je v mateřském kurzu GOC223.

## Klíčové rozlišení
- **`$filter` na serveru vs filtrování v pipeline doma** — server vrátí méně dat rychleji;
  `Where-Object` až jako poslední možnost.
- **Jedna stránka vs kompletní dataset** — bez průchodu `@odata.nextLink` skript lže.
- **Retry (429/5xx) vs fail-fast (jiné 4xx)** — opakovat má smysl jen u chyb, které
  odezní samy.
- **`code` (stabilní) vs `message` (může se změnit)** v chybovém objektu.

## Lab
Viz [`lab-graph-ingest.md`](lab-graph-ingest.md).

## Tipy
- **Tahák SPO API**: [`tips-spo-api.md`](tips-spo-api.md) — jak zjistit ID webu/site/seznamu,
  definici seznamu a knihovny, **interní názvy polí** a povolené hodnoty choice polí
  (Graph / PnP / REST vedle sebe). Budete ho potřebovat v Labu 5 i v praxi.
- `Retry-After` čtěte vždy z odpovědi — pevný `Start-Sleep -Seconds 30` je anti-pattern
  (a v ověření labu neprojde).
- OData `$filter` má vlastní syntax (`eq`, jednoduché uvozovky, case-sensitive) —
  skládejte dotaz v Graph Exploreru, kde chybu vrátí hned; do skriptu přenášejte ověřený.
- Počet výsledků skriptu vždy porovnejte s Graph Explorerem — skript bez `nextLink`
  smyčky selže tiše: vrátí méně dat bez jediné chybové hlášky.

## Zdroje (Microsoft)
- [Graph Explorer](https://developer.microsoft.com/en-us/graph/graph-explorer)
- [Use query parameters to customize responses](https://learn.microsoft.com/en-us/graph/query-parameters)
- [Paging Microsoft Graph data in your app](https://learn.microsoft.com/en-us/graph/paging)
- [Microsoft Graph throttling guidance](https://learn.microsoft.com/en-us/graph/throttling)
- [Microsoft Graph error responses and resource types](https://learn.microsoft.com/en-us/graph/errors)

## Stav produktu / delta
- Ověřit k datu běhu — service-specific throttling limity (počet requestů/sec per aplikace/tenant)
  se liší po workloadu a mění se; ověřit aktuální hodnoty na [Microsoft Graph service-specific throttling limits](https://learn.microsoft.com/en-us/graph/throttling-limits) před demonstrací.
