# Hands-on · První skill: „kontrola smlouvy před podpisem"

> Odhad: 20 min · Režim: živý tenant, vlastní web (Edit oprávnění)

## Cíl

Vytvořit skill, který zapisuje **firemní postup** (ne jednorázový prompt), najít ho
v `/Skills`, spustit — a podívat se, že výsledkem je obyčejný `SKILL.md` v knihovně
Agent Assets.

## Předpoklady

- Web, kde máte **Edit** oprávnění (pracovní web z D2/D3 stačí) a na něm 2–3 fiktivní
  dokumenty typu smlouva/směrnice.
- Copilot in SharePoint dostupný na webu (viz [`README.md`](README.md) — `KnowledgeAgentScope`).

## Kroky

1. **Zadání postupu**: v Copilotu na webu popsat přirozeným jazykem, co má skill dělat —
   např.: *„Vytvoř skill 'Kontrola smlouvy'. Postup: 1) najdi v dokumentu smluvní strany,
   předmět, cenu a datum účinnosti; 2) zkontroluj, že je uvedena doba trvání a výpovědní
   lhůta; 3) vypiš, co v dokumentu chybí, jako odrážkový seznam; 4) nehodnoť právní
   kvalitu, jen úplnost."*
2. **Přečíst draft** — Copilot nabídne návrh skillu. Projít ho jako kód z D1: dělá
   opravdu to, co chci? Nechybí krok? Upravit a uložit.
3. **Najít v seznamu**: v chatu napsat `/Skills` — nový skill musí být v seznamu.
4. **Spustit** nad jedním z dokumentů (vyvolat jménem nebo nechat Copilot vybrat).
   Porovnat výstup s tím, co postup předepisoval.
5. **Podívat se pod pokličku**: v knihovně **Agent Assets** otevřít
   `Skills/<nazev-skillu>/SKILL.md` — je to obyčejný Markdown. Přečíst, jak se váš
   postup zapsal.
6. **Jedna iterace**: upravit `SKILL.md` (nebo skill v chatu) — např. přidat požadavek
   na výstupní tabulku — a znovu spustit. Změřit rozdíl.

## Ověření

- [ ] Skill existuje, je vidět pod `/Skills` a spustí se.
- [ ] Účastník našel `SKILL.md` v knihovně Agent Assets a umí říct, co v něm je.
- [ ] Proběhla jedna iterace se změřeným efektem na výstup.
- [ ] Účastník umí vysvětlit, proč skill **nerozšiřuje oprávnění** (dělá jen to, co smí
      uživatel sám).

## Fallback

Není-li Copilot in SharePoint na webu k dispozici (RCD, `KnowledgeAgentScope`, vyčerpaný
limit preview), předvede lektor kroky 1–5 na svém webu a účastníci si připraví **text
postupu** — ten je plnohodnotný výstup, který se dá uložit později.

## Kam dál

- Postup, který má běžet nad víc weby a s pevnou konfigurací → agent
  ([`../../day-4/copilot-agents/comparison-agent-paths.md`](../../day-4/copilot-agents/comparison-agent-paths.md)).
- Postup, který má **něco zapsat** do systémů → akce (Power Automate, Copilot Studio),
  ne skill.
