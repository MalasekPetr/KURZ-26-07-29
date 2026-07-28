# Lab · Provozní monitoring — runbook

> Modul: monitoring · Odhad: 35 min · Režim: živý tenant (audit search) + runbook (deliverable)

## Cíl

Najít vlastní Copilot stopu v auditu a napsat incident runbook pro konkrétní scénář.

## Zadání incidentu

„V pondělí ráno hlásí uživatel, že Copilot v odpovědi ukázal obsah smlouvy, kterou by vidět neměl."

## Kroky

1. **Audit hands-on:** v Purview Audit vyhledej vlastní Copilot interakce z tohoto týdne (RecordType CopilotInteraction). Otevři jeden záznam a zapiš: k jakým zdrojům (AccessedResources) ses při labu dostal/a a jestli tam je web grounding (`BingWebSearch`).
2. **Runbook** (MD soubor, deliverable modulu) pro incident ze zadání — 5 fází z README diagramu; u každé: kdo (role), kde (portál/nástroj), co konkrétně udělá. Diagnóza musí obsahovat konkrétní audit dotaz (koho, jaký RecordType, jaké období) a triáž musí začít service health otázkou.
3. **Akční fáze:** vyber nástroj nápravy z týdne (RAC? RCD? blokace agenta? oprava permissions?) a odůvodni.
4. **Komunikace:** 2 řádky — co řekneš uživateli, co vedení.
5. Zkontroluj runbook proti otázce: „funguje i v sobotu ve 2 ráno, když nejsi k dispozici?" (role, ne jména; odkazy, ne paměť).

## Ověření

- [ ] Student našel vlastní CopilotInteraction záznam a přečetl AccessedResources.
- [ ] Runbook má 5 fází s rolemi a nástroji, audit dotaz je konkrétní.
- [ ] Náprava je zdůvodněná nástrojem z kurzu.
- [ ] Runbook projde testem „sobota 2:00".

## Fallback

- Audit role pro studenty nedostupná: instruktor promítne search nad vlastními interakcemi; studenti píšou runbook (jádro labu) beze změny.
