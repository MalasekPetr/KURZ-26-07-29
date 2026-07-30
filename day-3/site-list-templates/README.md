# Site templates & list templates — prostředí jako šablona

> Typ: povinný · Den: 3 · Odhad: 40 min výklad + 30 min lab

## Cíle
- Rozumět, proč se v moderním SharePointu weby **nekopírují**, ale **provisionují ze
  šablony** — a co ze starého světa („Save site as template", `.stp`) je mrtvé.
- Umět přečíst a napsat **site script** (JSON s akcemi) a zaregistrovat ho jako
  **site template** (dřív site design) — vlastními silami, bez vývojáře.
- Znát **list templates** (Microsoftem dodané i vlastní) a kdy stačí místo celého webu
  šablonovat jen seznam.
- Vědět, kdy JSON site script nestačí a nastupuje **PnP šablona** (`.pnp`/XML) —
  a jak z existujícího webu šablonu vytáhnout.

## Výklad

### Proč šablony: opakovatelnost místo klikání
Deset webů založených ručně je deset různých webů. Šablona je **kód** — verzovatelný,
review­ovatelný, opakovatelný. Zároveň je to jediná cesta, jak zajistit, že nový web
vznikne s nastavenou strukturou, oprávněními a metadaty bez toho, aby si to někdo
pamatoval.

Co je z klasického světa mrtvé: **„Save site as template"** (`.wsp`) na modern webech
není a nebude, stejně jako **`.stp` šablony seznamů** — kdo je hledá, hledá řešení
z roku 2010 (vazba na mrtvé vrstvy z [`../../day-1/api-landscape/`](../../day-1/api-landscape/)).

### Site script + site template (dřív site design)
**Site script** je JSON se seznamem **akcí** (`verb`) — vytvoř seznam, přidej sloupec,
nastav theme, spusť Flow, přidej navigaci. **Site template** je pojmenovaný obal nad
jedním nebo více skripty, který se nabídne uživateli při zakládání webu (nebo se aplikuje
na existující web).

```json
{
  "$schema": "schema.json",
  "actions": [
    {
      "verb": "createSPList",
      "listName": "Žádanky",
      "templateType": 100,
      "subactions": [
        { "verb": "setDescription", "description": "Žádanky o vybavení" },
        { "verb": "addSPField", "fieldType": "Text", "displayName": "Středisko", "isRequired": true },
        { "verb": "addSPField", "fieldType": "Choice", "displayName": "Stav",
          "choices": ["Nová", "Schválená", "Zamítnutá"], "addToDefaultView": true }
      ]
    },
    { "verb": "applyTheme", "themeName": "Modrá KÚZK" }
  ]
}
```

Registrace a použití (SPO modul i PnP ekvivalent):

```powershell
$script = Get-Content .\zadanky.json -Raw -Encoding utf8
Add-SPOSiteScript -Title "Žádanky – struktura" -Content $script
Add-SPOSiteDesign -Title "Web oddělení" -SiteScripts <script-id> -WebTemplate 68  # 68 = Communication
Invoke-SPOSiteDesign -Identity <design-id> -WebUrl "https://<tenant>.sharepoint.com/sites/<web>"

# PnP varianta
Add-PnPSiteScript -Title "Žádanky – struktura" -Content $script
Add-PnPSiteDesign -Title "Web oddělení" -SiteScriptIds <id> -WebTemplate CommunicationSite
Invoke-PnPSiteDesign -Identity <design-id> -WebUrl <url>
```

Limity, o kterých je dobré vědět předem: **100 site scriptů a 100 site templates na
tenant**, jeden skript má strop cca 300 akcí / 100 000 znaků, a spuštění je
**asynchronní** — akce se aplikují po vytvoření webu, takže „hned po založení tam ještě
nic není".

### List templates
Vedle webů lze šablonovat i jednotlivé seznamy. Tři úrovně:

1. **Microsoftem dodané list templates** — hotové (Sledování problémů, Onboarding…),
   dostupné při zakládání seznamu; nic se nekonfiguruje.
2. **Vlastní list design** — tentýž JSON site script (jen akce nad seznamem)
   zaregistrovaný přes `Add-SPOListDesign` / `Add-PnPListDesign`; objeví se
   uživatelům v „From your organization" při zakládání seznamu.
3. **Kopie z existujícího seznamu** — vytáhnout JSON z hotového webu a použít
   jako základ: `Get-PnPSiteScriptFromWeb -Url <url> -IncludeAll` (nebo `-Lists`),
   výstup upravit a zaregistrovat. **Nejrychlejší cesta k vlastní šabloně** — postavíte
   web klikáním, pak z něj vygenerujete kód.

### Kdy JSON nestačí — PnP šablony
Site script umí konečný výčet akcí. Co neumí (obsahové typy napříč hubem, permission
levels do detailu, obsah stránek, seznamy s daty), řeší **PnP provisioning šablona**:

```powershell
Get-PnPSiteTemplate -Out sablona.pnp -Handlers Lists,Fields,ContentTypes
Invoke-PnPSiteTemplate -Path sablona.pnp   # na cílový web
```

Rozdíl v jedné větě: **site template si vybere uživatel v UI, PnP šablonu spustí
skript.** V praxi se kombinují — site template pro samoobsluhu, PnP pro plnou
kontrolu při hromadném provisioningu (hloubka je téma navazujícího běhu,
[`../../backlog/provisioning-patterns/`](../../backlog/provisioning-patterns/)).

## Klíčové rozlišení
- **Site script (JSON, akce) vs site template (obal, který vidí uživatel)** — skript
  dělá práci, template ho zpřístupní.
- **Site template (self-service při zakládání) vs PnP šablona (spustí skript)** —
  samoobsluha vs plná kontrola; často obojí vedle sebe.
- **List template vs celý site template** — když potřebujete opakovat jeden seznam,
  nešablónujte celý web.
- **Modern vs klasické šablony** — „Save site as template" a `.stp` jsou mrtvé;
  ekvivalent dnes je JSON script nebo PnP šablona.
- **Asynchronní aplikace** — po založení webu akce ještě běží; skript, který hned
  ověřuje výsledek, může vidět nedokončený stav.

## Lab
Viz [`lab-site-template.md`](lab-site-template.md).

## Tipy
- Nejrychlejší start vlastní šablony: naklikat web, pak `Get-PnPSiteScriptFromWeb` —
  dostanete hotový JSON, který jen upravíte (a rovnou vidíte, jak se akce jmenují).
- JSON pro šablony **s diakritikou vždy v UTF-8** (`Get-Content -Raw -Encoding utf8`) —
  jinak z „Žádanky" bude „Å½Ã¡danky" v názvu seznamu (pravidlo z D1).
- Šablony verzujte v gitu vedle skriptů — je to kód, ne konfigurace v portálu.
- Limit 100+100 na tenant není vysoký: neregistrujte testovací pokusy „nadvakrát",
  po experimentech uklízejte (`Remove-SPOSiteScript`, `Remove-SPOSiteDesign`).

## Zdroje (Microsoft)
- [SharePoint site design and site script overview](https://learn.microsoft.com/en-us/sharepoint/dev/declarative-customization/site-design-overview)
- [Site design JSON schema](https://learn.microsoft.com/en-us/sharepoint/dev/declarative-customization/site-design-json-schema)
- [Create list templates](https://learn.microsoft.com/en-us/sharepoint/dev/declarative-customization/list-designs)
- [PnP provisioning — Get-PnPSiteTemplate](https://pnp.github.io/powershell/cmdlets/Get-PnPSiteTemplate.html)

## Stav produktu / delta
> [!IMPORTANT]
> Názvosloví se přejmenovalo: **„site design" → „site template"** v UI a dokumentaci,
> cmdlety ale zůstaly `*-SPOSiteDesign` / `*-PnPSiteDesign`. Až uslyšíte „site design",
> jde o totéž.

> [!WARNING]
> Ověřit k datu běhu — limity (100 scriptů/100 templates, strop akcí), sada podporovaných
> `verb` akcí a hodnoty `-WebTemplate` se mění; před během zkontrolovat JSON schema
> a projít lab na kurzovním tenantu.
