# Výklad · Srovnání modelů: prebuilt × structured × freeform (× unstructured)

> Modul: document-processing · Doplněk k „Co rodina obsahuje" v README · Stav ověřen k 2026-07
> Prostředí: viz [`../../environment.md`](../../environment.md) · Názvosloví: [`../../GLOSSARY.md`](../../GLOSSARY.md)

Čtyři typy modelů vypadají v UI jako varianty jedné funkce, ale **dělí se na dva světy**: prebuilt a unstructured žijí čistě v Microsoft 365; **structured a freeform jsou AI Builder modely z Power Platform** — jiné prostředí, jiná měna, jiná regionální dostupnost i jazyková podpora.

## Srovnávací matice

| | **Prebuilt** | **Structured** | **Freeform** | **Unstructured** |
| --- | --- | --- | --- | --- |
| **K čemu** | hotové extraktory s pevným schématem: smlouvy, faktury, účtenky, citlivé údaje (PII), simple doc | formuláře s **fixním layoutem** (faktury, objednávky) — pole na stejném místě | volný text **bez struktury** (dopisy, faxy, skeny) — jen extrakce, **neklasifikuje** | dokumenty s různým layoutem, ale opakovanými frázemi — **klasifikace + extrakce** |
| **Kde se tvoří** | SharePoint (content center / lokálně v knihovně), **bez trénování** | **AI Builder** (vstup ze SP knihovny) | **AI Builder** (vstup ze SP knihovny) | SharePoint content center |
| **Platforma / běh** | Microsoft 365, všechny regiony | **Power Platform environment** (default; custom s Dataverse DB podporovaný); dostupnost dle regionů Power Platform | totéž co structured | Microsoft 365, všechny regiony |
| **Účtování** | M365 PAYG ($0,01/txn) | **M365 PAYG nejde** — AI Builder kredity (3 500/licence/měsíc; 1 M kreditů ≈ 10 000 stran) → přechod na Copilot Credits, viz delta | totéž co structured | M365 PAYG ($0,005/txn) |
| **Jazyky** | dle konkrétního modelu (viz stránka modelu) | **100+** — nejširší | **40+** | **40+** |
| **Trénink** | žádný | pdf/jpg/png, celkem 50 MB / 500 stran | pdf/jpg/png, celkem 50 MB / 500 stran | 5–10 souborů vč. negativních příkladů; 20+ typů souborů; text ořez 64 000 znaků, OCR skeny max 20 stran |
| **Managed metadata** | — | ne | ne | **ano** (jediný) |
| **Purview labely** (retention/sensitivity) | ano | ano | ano | ano |

## Nosné rozlišení: M365 svět vs. Power Platform svět

- **Prebuilt + unstructured** = celé v M365: tvorba v SharePointu, účtování přes Document processing PAYG (konfigurace — plný kurz GOC224), dostupnost všude.
- **Structured + freeform** = AI Builder: SharePoint je jen „okno" — model reálně vzniká a běží v **Power Platform environmentu** (default environment; custom environment vyžaduje Dataverse databázi). Důsledky:
  - **Účtování jde mimo M365 PAYG** — transakční cena „not applicable"; spotřebovává kredity Power Platformy. Glosářová dvojice „Document processing PAYG vs. Copilot Credits" tu dostává hvězdičku: structured/freeform patří kreditové větvi, i když jsou v rodině Document processing.
  - **Regionální dostupnost dle Power Platform** (ne M365) — v neobvyklých regionech ověřit.
  - Governance modelů = Power Platform admin center (environmenty, DLP, kapacita), ne SharePoint admin.
- **Jazykový paradox k zapamatování**: nejvíc jazyků nemá M365 větev, ale structured (100+); freeform i unstructured mají 40+. Pro CZ dokumenty všechny tři custom typy fungují — ale u prebuilt modelů jazykovou podporu ověřit per model (např. faktury/účtenky mají vlastní seznamy).

## Rozhodovací postup

1. **Pokrývá to prebuilt?** (faktura/smlouva/účtenka/PII) → prebuilt, nic se netrénuje, M365 PAYG. Fixní schéma — pokud potřebuješ pole navíc, dál.
2. **Fixní layout formuláře?** → structured (pozor: Power Platform větev, kredity). 
3. **Volný text, stačí extrakce?** → freeform (tatáž větev).
4. **Různé layouty + potřeba klasifikace nebo managed metadata?** → unstructured (M365 větev, PAYG).
5. A před vším: **stačil by autofill sloupec?** (viz README — začínat autofillem, model až když pravidel přibývá).

## Zdroje (Microsoft)

[Model types overview](https://learn.microsoft.com/en-us/microsoft-365/documentprocessing/model-types-overview) · [Compare custom models](https://learn.microsoft.com/en-us/microsoft-365/documentprocessing/difference-between-document-understanding-and-form-processing-model) · [Prebuilt models](https://learn.microsoft.com/en-us/microsoft-365/documentprocessing/prebuilt-overview) · [AI Builder form processing requirements (jazyky)](https://learn.microsoft.com/en-us/ai-builder/form-processing-model-requirements) · [End of AI Builder credits](https://learn.microsoft.com/en-us/ai-builder/endofaibcredits)

## Stav produktu / delta

> [!WARNING] Ověřit k datu běhu — stav k 2026-07.
> **AI Builder kredity končí** (oznámeno 10/2025): od 11/2025 stop prodeje capacity add-onů novým zákazníkům; k **1. 11. 2026** konec obnov add-onů a odebrání seeded kreditů z premium licencí. Náhrada = **Copilot Credits** (dual-mode: nejdřív se čerpají AI Builder kredity, pak Copilot Credits; bez obou se funkce zablokuje; žádná konverze zůstatků). Pro structured/freeform to znamená: kreditová větev se slévá do stejné měny jako Copilot Studio — sledovat [endofaibcredits](https://learn.microsoft.com/en-us/ai-builder/endofaibcredits) a AI Builder capability rate tabulku. Jazykové počty (40+/100+) ověřit proti AI Builder requirements stránce.
