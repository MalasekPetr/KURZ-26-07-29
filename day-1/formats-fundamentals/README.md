# Formáty dat (JSON, YAML, XML, CSV) a PowerShell od nuly

> Typ: povinný · Den: 1 · Odhad: 60 min výklad + 60 min Lab 1

## Cíle
- Přečíst a napsat jednoduchý **JSON** (objekt, pole, typy hodnot) a poznat ho
  v odpovědích Graphu i v konfiguračních souborech (`tasks.json`).
- Přečíst **YAML** a vědět, kde se s ním potká (CI/CD pipeline definice) — pasivní
  znalost, nepíšeme ho.
- Poznat **XML** (PnP šablony, CAML) a umět pracovat s **CSV**
  (`Import-Csv`/`Export-Csv`) — tabulární můstek mezi skripty a Excelem.
- Vědomě pracovat s **UTF-8**: české prostředí a data SPO = diakritika všude;
  jedno kódování od souboru přes API po výstup.
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
  "displayName": "Jana Nováková",
  "jobTitle": "Referentka",
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
displayName: Jana Nováková
memberOf:
  - web-dev
  - web-test
enabled: true
```

### XML a CSV — starší sourozenci (poznat a použít)
**XML** je předchůdce JSON na stejné pozici (strukturovaná data, závorky nahrazují
tagy). Ve světě SPO ho potkáte v **PnP provisioning šablonách** a v **CAML dotazech**
(dotazovací jazyk klasického SharePoint API) — v tomto běhu ho jen čteme a poznáváme;
hloubka patří k provisioning tématům navazujícího běhu. **CSV** je nejjednodušší
tabulka v textu — a v praxi můstek mezi skripty a Excelem: `Import-Csv` udělá
z řádků objekty pro pipeline, `Export-Csv` z objektů report, který si otevře
vedoucí. Pozor na dvě věci u českého Excelu: oddělovač (`-UseCulture` respektuje
středník českého prostředí) a kódování — viz hned další sekce.

### UTF-8 a čeština — jedno kódování všude
Pracujeme v českém prostředí s daty SPO — diakritika je v každém displayName,
názvu webu i sloupci. Pravidla, aby přežila celou cestu:

1. **API je v pohodě**: Graph i SharePoint REST mluví UTF-8 vždy (JSON je UTF-8
   z definice). Problémy vznikají až na hranici se **soubory a konzolí**.
2. **VS Code i PowerShell 7 mají UTF-8 jako default** — kurz je PS7-first, takže
   výchozí stav je správně. Přesto ve skriptech pište kódování **explicitně**:
   `Get-Content -Encoding utf8`, `Set-Content -Encoding utf8` — skript pak přežije
   i spuštění ve Windows PowerShell 5.1, kde default UTF-8 není.
3. **CSV pro Excel = `utf8BOM`**: `Export-Csv -Encoding utf8BOM -UseCulture` —
   bez BOM český Excel soubor otevře jako ANSI a z „Nováková" je „NovÃ¡kovÃ¡".
   To je nejčastější UTF-8 nehoda v praxi vůbec.
4. **Kontrola po ruce**: stavový řádek VS Code ukazuje kódování souboru (má stát
   UTF-8); rychlý test diakritiky = round-trip v Labu 1.

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
   co maže nebo mění. A semínko na D3: ne každá chyba je stejná — některé jsou
   **přechodné** (za chvíli zmizí samy) a některé **trvalé** (opakování nepomůže);
   jak je rozlišit a co s tím, přijde u Graph API.
6. **Most k JSON**: `Get-Content data.json | ConvertFrom-Json` udělá z textu objekty pro
   pipeline; `ConvertTo-Json` opačně.

### Workflow s Copilot Chat — navrhne, já přečtu, otestuji
M365 Copilot Chat (free, s firemní ochranou dat — viz
[`../vscode-copilot-env/`](../vscode-copilot-env/)) umí PowerShell vygenerovat z českého
zadání. Umí si ale i **vymýšlet** — neexistující cmdlety a parametry vypadají věrohodně
a spadnou až za běhu. Proto každá konverzace začíná **priming promptem**
([`copilot-priming-prompt.md`](copilot-priming-prompt.md)), který fantazii omezí a nutí
model přiznat nejistotu. Pravidlo kurzu: **prompt obsahuje akceptační kritéria, člověk
čte každý řádek před spuštěním a nic citlivého do promptu nepatří** (tenant ID, hesla,
thumbprinty).
Copilot je akcelerátor učení — vygenerovaný skript je učebnice, kterou si necháte
vysvětlit („co dělá řádek 3?").

## Klíčové rozlišení
- **JSON vs YAML** — stejná data, jiný zápis; JSON pro API a konfiguraci, YAML pro
  pipelines; my píšeme JSON, YAML čteme.
- **XML vs JSON** — stejná role, starší generace; XML čteme v PnP šablonách a CAML,
  nepíšeme. **CSV** = tabulka pro výměnu s Excelem (`Import-Csv`/`Export-Csv`).
- **UTF-8 vs „ono to nějak dopadne"** — kódování se ve skriptu píše explicitně;
  CSV pro Excel vždy `utf8BOM`, jinak česká diakritika nepřežije.
- **Objekt vs text** — PowerShell pipeline nese objekty s vlastnostmi; `Get-Member` je
  odpověď na „co s tím můžu dělat".
- **Skript vs příkaz** — příkaz je jednorázový, skript s `param()` a `try/catch` je
  nástroj; rozdíl mezi „bojím se to pustit" a „vím, co udělá".
- **Copilot návrh vs otestovaný kód** — návrh je vstup k přečtení a testu, ne hotový
  výstup; odpovědnost nese člověk.

## Lab
Viz [`lab-first-script.md`](lab-first-script.md).

## Tipy
- Nejčastější JSON chyba: čárka za posledním prvkem pole nebo chybějící uvozovky —
  editor ji podtrhne, naučte se to podtržení číst.
- Dvě otázky, které vás vždy zachrání: `$objekt | Get-Member` („co s tím můžu dělat?")
  a `Get-Help <cmdlet> -Examples` („jak se to volá?").
- U vygenerovaného kódu se ptejte Copilota „vysvětli, co dělá řádek X" tak dlouho,
  dokud nezbude jediný nejasný řádek — je to učebnice na míru, ne jen generátor.

## Zdroje (Microsoft)
- [PowerShell 101](https://learn.microsoft.com/en-us/powershell/scripting/learn/ps101/00-introduction)
- [Discover objects, properties, and methods](https://learn.microsoft.com/en-us/powershell/scripting/learn/ps101/03-discovering-objects)
- [ConvertFrom-Json](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/convertfrom-json)
- [Import-Csv / Export-Csv](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/import-csv)
- [about_Character_Encoding (PowerShell)](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_character_encoding)
- [Introducing JSON (json.org)](https://www.json.org/json-en.html)

## Stav produktu / delta
> [!WARNING]
> Ověřit k datu běhu — vstupní stránka a přihlášení M365 Copilot Chat free se mění
> (aktuálně `https://m365.cloud.microsoft/chat`); ověřit, že je dostupný na kurzovním
> tenantu, den před během.
