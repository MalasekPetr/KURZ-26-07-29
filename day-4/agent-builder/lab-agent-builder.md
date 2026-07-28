# Lab · HR Asistent v Agent Builderu (hands-on)

> Modul: agent-builder · Odhad: 40 min · Režim: **studenti hands-on** (dle dostupnosti); simulace fallback
> Scénář a data: [`../copilot-agents/scenario-hr-agent.md`](../copilot-agents/scenario-hr-agent.md) · Návrh: [`../copilot-agents/lab-agent-design.md`](../copilot-agents/lab-agent-design.md)

## Cíl

Postavit agenta **HR Asistent** v Agent Builderu nad daty zaměstnanců a na vlastní kůži vidět, kde je jeho strop — analytiku, kterou neumí, prolomí zítra Copilot Studio (D5).

## Předpoklady

- Data na HR webu (`/sites/hr-demo`): list `Zaměstnanci` + knihovny `Certifikáty`/`Smlouvy`.
- Agent Builder dostupný pro studenty (dostupnost v PAYG tenantu ověřuje lektor).
- Návrh instrukcí z [`../copilot-agents/lab-agent-design.md`](../copilot-agents/lab-agent-design.md).

## Kroky

### Část A — evaluační plán (psací)

1. Napiš **5 testovacích dotazů**. Povinně:
   - **lookup** („údaje a manažer osobního čísla 10018"),
   - **hledání v souboru** („najdi podepsanou pracovní smlouvu pro 10024"),
   - **analytický** („komu propadá certifikát do 30 dnů") — očekávej, že **selže**,
   - **negativní test** (na co NEMÁ odpovídat),
   - **test hranice práv** (řadový zaměstnanec chce cizí mzdový výměr → „no response").
2. Ke každému **očekávaný výsledek** + **kritérium úspěchu** (kolik z 5 musí projít).

### Část B — stavba v Agent Builderu

3. V Copilot appce → **Vytvořit agenta**; instrukce z části A (D4: purpose → guidelines). **Instrukce ≠ knowledge.**
4. **Knowledge:** list `Zaměstnanci` + knihovny `Certifikáty`/`Smlouvy`.
5. Spusť **5 testů**, zapiš skóre (x/5).
6. Ověř strop: dotaz 1 a 2 projdou, **analytický dotaz 3 nedá spolehlivý výčet** (Builder jen prioritizuje). Zapiš, co přesně vrátil.
7. Udělej **jednu iteraci** instrukcí/popisu a změř efekt na nejhorší test.

### Reflexe — most do D5

8. Analytický dotaz neprošel. Kdo ho umí? **Zítra Copilot Studio** nad týmiž daty.

## Ověření

- [ ] Evaluační plán má lookup, hledání v souboru, analytický, negativní a test hranice práv.
- [ ] Zapsané skóre (x/5) + jedna iterace se změřeným efektem.
- [ ] Zaznamenáno, že analytický dotaz selhal a proč (Builder neagreguje).
- [ ] Test hranice práv dopadl „no response" (permission trimming).

## Fallback

- Agent Builder nedostupný: část A je plnohodnotný deliverable; instruktor postaví HR agenta v demu, třída píše predikce a pak se ověří.

## PAYG poznámka

Každý testovací dotaz = Copilot Credits. 5 promyšlených testů + 1 iterace, ne brute-force (viz `../../environment.md`).
