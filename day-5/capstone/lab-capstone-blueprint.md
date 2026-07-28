# Lab · Capstone — rollout blueprint

> Modul: capstone · Odhad: 60 min + 30 min prezentace · Režim: individuální design dokument (MD)

## Cíl

Sestavit rollout blueprint pro vlastní organizaci a obhájit ho ve 3 minutách.

## Kroky

1. Založ `blueprint.md` na svém webu; struktura = 7 sekcí z README diagramu.
2. **Kontext** (5 min): velikost, počet webů, licenční stav, cíl nasazení — 5 řádků.
3. **Konfigurace** (10 min): PAYG model(y) — které metry a proč; Copilot scope (jaké weby, jak řízeno); search/zdroje (RSS/RCD/autoritativní weby). Konkrétně, ne „zapneme Copilota".
4. **Automatizace** (10 min): 1 proces k automatizaci (vzor: dávkový sync z D3, nebo vytěžování faktur — plný kurz GOC224) + cesta realizace a schvalování; 1 agent (návrh z dopoledne se sem vkládá!).
5. **Governance** (10 min): kdo má AI Administrator, audit + runbook odkaz, least-privilege checklist z D3; SAM politiky a backup/archiv strategie doplnit dle plného kurzu GOC224. Tabulka mapy vrstev z D5 jako kostra.
6. **Registr rizik** (10 min): 5 povinných kategorií z README, formát riziko → dopad → mitigace (nástroj!) → vlastník.
7. **Change enablement + roadmapa** (10 min): 3 komunikační kroky; 3 fáze s go/no-go branami.
8. **Prezentace** (3 min/student): kontext → 1 rozhodnutí, na které jsi hrdý/á → 1 největší riziko. Ostatní: 1 otázka.

## Ověření

- [ ] Všech 7 sekcí; každá odkazuje na konkrétní nástroj/politiku z kurzu (ne obecné fráze).
- [ ] Rizika mají mitigace nástroji z kurzu a vlastníky.
- [ ] Roadmapa má měřitelné go/no-go brány.
- [ ] Prezentace proběhla a ustála otázku.

## Fallback

- Student bez vlastní organizace (konzultant, student): použije zadání fiktivní firmy z D4 labů (200 lidí, 60 webů).
- Časová tíseň: sekce 3–5 jsou jádro; 6–7 lze dopsat po prezentacích.
