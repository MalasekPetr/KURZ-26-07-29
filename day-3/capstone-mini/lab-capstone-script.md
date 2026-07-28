# Lab 5 · Mini-capstone: od zadání k otestovanému skriptu

> Odhad: 75 min · Režim: živý tenant (zápis jen nad vlastním `-dev` webem)

## Cíl

Účastník samostatně dodá funkční, otestovaný a zdokumentovaný skript k vybrané úloze
(viz [`README.md`](README.md)) — s Copilot Chatem jako asistentem a s identitou,
permissions a testovací disciplínou z celého kurzu.

## Předpoklady

- App registrace + certifikát z D2 (Laby 2–3), `Connect-CourseTarget` wrapper,
  `Get-AllGraphResults` z Labu 4.
- Repozitář z D1 s `lint`/`test` tasky; přihlášený M365 Copilot Chat.
- Tahák [`../graph-fundamentals/tips-spo-api.md`](../graph-fundamentals/tips-spo-api.md)
  — ID webů a seznamů, interní názvy polí a choices pro váš skript (a do promptu).

## Kroky

1. **Rozmyslet před promptem** (10 min, na papír): co přesně má skript dělat, jaká data
   čte (který endpoint?), pod jakou identitou poběží (delegated/app-only? proč?),
   jaké permission potřebuje (stačí ta, co mám?), co nesmí udělat.
2. **Nová konverzace = priming prompt** ([`../../day-1/formats-fundamentals/copilot-priming-prompt.md`](../../day-1/formats-fundamentals/copilot-priming-prompt.md)),
   pak **prompt s akceptačními kritérii** — česky, včetně požadavků na `param()`,
   `try/catch`, stránkování a strukturovaný výstup. Bod 1 je v podstatě hotový prompt.
3. **Číst návrh řádek po řádku**; nejasné řádky nechat vysvětlit; zkontrolovat proti
   checklistu z D1 (hardcoded identifikátory, error handling, `-WhatIf` u zápisu).
4. **Testovat od bezpečného konce**: nejdřív syntaxe/lint task, pak čtecí část nad
   `-dev` webem, u úlohy 3 nejdřív `-WhatIf` běh a diff toho, co by se stalo.
5. **Spustit naostro** (úloha 3 jen nad vlastním `-dev` webem) a uložit výstup.
6. **Commit** do repozitáře z D1: skript + krátký README blok (k čemu, jaká identita,
   jaká permissions, jak spustit) + prompt, ze kterého vznikl (bez citlivých údajů).

## Ověření

- [ ] Skript běží pod app registrací účastníka a nepožaduje žádné permission navíc
      proti stavu po hardening auditu (D2).
- [ ] Stránkování: výsledek je úplný i při více stránkách odpovědi (namátková kontrola
      instruktorem proti Graph Exploreru).
- [ ] Účastník umí u libovolného řádku říct, co dělá a proč tam je.
- [ ] Zapisující úloha proběhla nejdřív s `-WhatIf` a jen nad vlastním `-dev` webem.
- [ ] Výstupní soubory jsou UTF-8 (CSV pro Excel `utf8BOM`) a česká diakritika
      z dat SPO přežila až do výstupu.
- [ ] Commit obsahuje skript, dokumentační blok i použitý prompt.

## Fallback

- Kdo nestíhá, dokončí jen čtecí část úlohy — ověření bodů 1–3 platí i pro ni.
- Při nedostupnosti Copilot Chatu: účastník píše skript skládáním z kusů Labů 1–4
  (všechny stavební bloky už existují) — pomalejší, ale plnohodnotný průchod.
