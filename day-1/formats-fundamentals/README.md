# JSON, YAML a PowerShell od nuly

> Typ: povinný · Den: 1 · Odhad: 60 min výklad + 60 min Lab 1

## Cíle
- Přečíst a napsat jednoduchý **JSON** (objekt, pole, typy hodnot) a poznat ho
  v odpovědích Graphu i v konfiguračních souborech (`tasks.json`).
- Přečíst **YAML** a vědět, kde se s ním potká (CI/CD pipeline definice) — pasivní
  znalost, nepíšeme ho.
- Ovládnout minimum PowerShellu jako **jazyka**: cmdlet (`Verb-Noun`), proměnná,
  pipeline objektů, `Get-Help`/`Get-Member`, hashtable, `param()`, `try/catch`,
  `ConvertFrom-Json`.
- Vygenerovat si s **M365 Copilot Chat** první skript, přečíst ho, otestovat a upravit —
  workflow, který se ponese celým kurzem.

## Výklad

### JSON — lingua franca API
Všechno, co v tomto kurzu poteče po drátě nebo bude ležet v konfiguraci, je JSON:

```json
{
  "displayName": "Jana Novakova",
  "jobTitle": "Referent",
  "memberOf": ["web-dev", "web-test"],
  "enabled": true,
  "manager": null
}
```

Pět stavebních kamenů: **objekt** `{}` (pojmenované hodnoty), **pole** `[]` (seznam),
**string** v uvozovkách, **číslo/bool** bez uvozovek, **null**. Nic víc JSON neumí — a
proto ho umí číst všechno. Kde ho potkáte: odpověď každého Graph volání (viz demo
z [`../api-landscape/`](../api-landscape/)), `tasks.json` ve VS Code, šablony webů.

### YAML — JSON pro lidi (jen číst)
Stejná data, zápis odsazením místo závorek. Potkáte ho v CI/CD pipeline definicích
(GitHub Actions, Azure DevOps) a ve front-matter dokumentů. Záludnost: **odsazení nese
význam** (mezery, nikdy tabulátor). V kurzu YAML jen čteme.

```yaml
displayName: Jana Novakova
memberOf:
  - web-dev
  - web-test
enabled: true
```

### PowerShell — objekty, ne text
1. **Cmdlet = sloveso-podstatné jméno**: `Get-Date`, `Get-ChildItem`, `ConvertFrom-Json`.
   Schválená slovesa dělají jazyk předvídatelným — když umíte `Get-`, uhodnete `Set-`.
2. **Pipeline posílá objekty**, ne řádky textu: `Get-ChildItem | Where-Object Length -gt 1MB`
   filtruje podle *vlastnosti* souboru. Dvě sebezáchranné otázky: `Get-Member` („co ten
   objekt umí?") a `Get-Help <cmdlet> -Examples` („jak se to volá?").
3. **Proměnné a hashtable**: `$web = "dev"`, `$config = @{ Name = "web-dev"; Owner = "jana" }`.
4. **Skript s parametry**: `param([string]$Path)` na začátku souboru dělá ze skriptu
   znovupoužitelný nástroj místo jednorázové vložky.
5. **Chyby pod kontrolou**: `try { ... } catch { ... }` — skript, který umí říct „tohle
   se nepovedlo a proč", je skript, kterého se nemusíte bát. K tomu `-WhatIf` u všeho,
   co maže nebo mění.
6. **Most k JSON**: `Get-Content data.json | ConvertFrom-Json` udělá z textu objekty pro
   pipeline; `ConvertTo-Json` opačně.

### Workflow s Copilot Chat — navrhne, já přečtu, otestuji
M365 Copilot Chat (free, s firemní ochranou dat — viz
[`../vscode-copilot-env/`](../vscode-copilot-env/)) umí PowerShell vygenerovat z českého
zadání. Pravidlo kurzu: **prompt obsahuje akceptační kritéria, člověk čte každý řádek
před spuštěním a nic citlivého do promptu nepatří** (tenant ID, hesla, thumbprinty).
Copilot je akcelerátor učení — vygenerovaný skript je učebnice, kterou si necháte
vysvětlit („co dělá řádek 3?").

## Klíčové rozlišení
- **JSON vs YAML** — stejná data, jiný zápis; JSON pro API a konfiguraci, YAML pro
  pipelines; my píšeme JSON, YAML čteme.
- **Objekt vs text** — PowerShell pipeline nese objekty s vlastnostmi; `Get-Member` je
  odpověď na „co s tím můžu dělat".
- **Skript vs příkaz** — příkaz je jednorázový, skript s `param()` a `try/catch` je
  nástroj; rozdíl mezi „bojím se to pustit" a „vím, co udělá".
- **Copilot návrh vs otestovaný kód** — návrh je vstup k přečtení a testu, ne hotový
  výstup; odpovědnost nese člověk.

## Lab
Viz [`lab-first-script.md`](lab-first-script.md).

## Zdroje (Microsoft)
- [PowerShell 101](https://learn.microsoft.com/en-us/powershell/scripting/learn/ps101/00-introduction)
- [Discover objects, properties, and methods](https://learn.microsoft.com/en-us/powershell/scripting/learn/ps101/03-discovering-objects)
- [ConvertFrom-Json](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/convertfrom-json)
- [Introducing JSON (json.org)](https://www.json.org/json-en.html)

## Stav produktu / delta
> [!WARNING]
> Ověřit k datu běhu — vstupní stránka a přihlášení M365 Copilot Chat free se mění
> (aktuálně `https://m365.cloud.microsoft/chat`); ověřit, že je dostupný na kurzovním
> tenantu, den před během.
