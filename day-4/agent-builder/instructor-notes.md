# Instructor notes — Agent Builder

## Go/no-go

- **Dostupnost v PAYG tenantu**: docs říkají „M365 Copilot licence NEBO tenant s PAYG pro Copilot Studio" — ověřit, že PAYG cesta u nás reálně Agent Builder zpřístupňuje i studentům. Určuje, jestli staví studenti (část B), nebo jen instruktor v demu (fallback).
- Demo stavět nad **instruktorským** webem, ne studentskými (práva, překvapení).

## Tripwires

- **Instrukce ≠ knowledge** (XPIA) — studenti rádi nacpou směrnici do instrukcí.
- **Negativní test** v evaluačním plánu je povinný — bez něj se agent „vždycky povede".
- Strop ukázat naživo: analytický dotaz („komu propadá certifikát do 30 dnů") Builder **neagreguje** — jen prioritizuje. To je most na Copilot Studio (D5), ne selhání labu.
- Sdílením agenta s embedded soubory sdílíš i obsah — zmínit u publikace.

## Vazby

- Zpět: Agent Instructions (D2), mapa cest a srovnání ([`../copilot-agents/`](../copilot-agents/README.md)).
- Dopředu: SharePoint agents (další blok, limit 1 zdroj) → Agents Toolkit → správa v D5 `copilot-admin`; analytika ve Studiu (D5).
