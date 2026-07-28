# Lab · Návrh agenta a plán vyhodnocení

> Modul: copilot-agents · Odhad: 40 min · Režim: návrh (papír/MD) + hands-on dle dostupnosti (Agent Builder)

## Cíl

Navrhnout agenta pro reálný scénář ze své praxe — instrukce, knowledge, cesta tvorby — a plán, jak poznáš, že funguje.

> [!NOTE] Worked example
> Pokud nemáš vlastní scénář, použij **HR Asistenta** — [`scenario-hr-agent.md`](scenario-hr-agent.md) (list zaměstnanců + knihovny propojené osobním číslem). Jeho stavbu řeší nástrojové bloky: [Agent Builder](../agent-builder/lab-agent-builder.md), [SharePoint agents](../../day-5/sharepoint-agents/lab-sharepoint-agent.md), [Agents Toolkit](../../day-5/agents-toolkit/lab-toolkit-agent.md).

## Kroky

### Část A — návrh (všichni)

1. Vyber scénář ze své práce (onboarding kolegů, FAQ oddělení, směrnice…). Jedna věta: co agent umí a pro koho.
2. Napiš **Agent Instructions** (struktura z D4: purpose → guidelines → skills; max ~1 500 znaků pro lab). Instrukce ≠ knowledge — obsah patří do zdrojů!
3. Urči **knowledge**: které weby/knihovny, kdo na ně má práva (agent nepřekročí permissions uživatele).
4. Vyber **cestu tvorby** z tabulky v README + 2 argumenty (kdo bude tvořit a udržovat, kam se distribuuje).
5. **Plán vyhodnocení**: 5 testovacích promptů s očekávanou odpovědí — z toho aspoň 1 negativní test („na tohle NEMÁ odpovídat / nemá vidět data X") a 1 test hranice práv (uživatel bez přístupu ke zdroji). Kritérium úspěchu: kolik z 5 musí projít.

### Část B — hands-on (dle dostupnosti)

6. **Agent Builder dostupný:** postav agenta z části A (instrukce + knowledge = vlastní web), spusť 5 testů z plánu, zapiš výsledky.

> Skills hands-on **jsou možné i pro studenty** — Copilot in SharePoint empiricky funguje i na PAYG (ověřeno 2026-07-17; MS uvádí license-only, docs lag). Deep-dive a stavba zítra v D5 `skills`.

## Ověření

- [ ] Instrukce drží strukturu a neobsahují obsah (ten je v knowledge).
- [ ] Volba cesty má 2 argumenty vč. údržby.
- [ ] Evaluační plán má negativní test a test hranice práv.
- [ ] Část B: výsledky 5 testů zapsané (kolik prošlo, co ne a proč).

## Fallback

- Není Agent Builder: část A je plnohodnotný deliverable; 2–3 návrhy projít nahlas, instruktor vybraný postaví v demu.
