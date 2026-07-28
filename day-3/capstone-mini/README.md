# Mini-capstone: vlastní skript s Copilot Chat

> Typ: povinný · Den: 3 (závěr automatizační části) · Odhad: 15 min zadání + 75 min Lab 5 + 30 min prezentace & next steps

## Cíle
- Samostatně projít celý workflow kurzu bez vodítek: úloha → prompt s akceptačními
  kritérii → čtení návrhu → test → běh nad živým tenantem pod správnou identitou.
- Prokázat, že tři dny drží pohromadě: API mapa (D1), identita a certifikát (D2),
  Graph dotaz se stránkováním (D3).
- Odnést si osobní „first automation" plán: kterou reálnou úlohu z vlastní firmy
  zautomatizuji jako první a co k tomu potřebuji.

## Průběh

### Zadání (15 min)
Každý účastník si vybere jednu úlohu (nebo přinese vlastní — vítáno):
1. **Inventura webů** — seznam webů tenantu s vlastníkem a poslední aktivitou, výstup
   JSON + čitelná tabulka; označit weby bez aktivity za N dní.
2. **Report členství** — pro zadaný web vypsat, kdo má jaký přístup (vlastníci/členové/
   návštěvníci), výstup pro vedoucího, který chce vědět „kdo vidí co".
3. **Doplnění metadat** — nad pracovním webem `-dev` dávkově založit/aktualizovat
   položky seznamu z JSON vstupu (jediná zapisující úloha — jen nad vlastním webem,
   s `-WhatIf` režimem).

Všechny úlohy běží pod app registrací z D2 (delegated, případně app-only cert pro
čtecí varianty) nad pracovními weby z Labu 3.

### Lab (75 min)
Viz [`lab-capstone-script.md`](lab-capstone-script.md).

### Prezentace & next steps (30 min)
Tři dobrovolníci ukáží skript a hlavně **prompt a proces** (co Copilot navrhl špatně
a jak to odhalili). Závěrečné kolečko: každý řekne svou „first automation" — jednu
úlohu z vlastní praxe, kterou příští týden zkusí. Instruktor uzavře mapou next steps:
plné kurzy GOC223/GOC224 (migrace, provisioning, Azure integrace — backlog tohoto
repa), dny 4–5 tohoto kurzu (AI & Copilot agenti).

## Klíčové rozlišení
- **Skript pro kurz vs skript pro firmu** — v labu je vše dovoleno číst, ale zapisovat
  jen nad vlastním `-dev` webem s `-WhatIf`; stejná disciplína (nejužší scope, test,
  audit trail) je doma podmínka důvěry, kterou automatizace teprve buduje.
- **Copilot jako akcelerátor vs náhrada porozumění** — capstone se hodnotí podle toho,
  co účastník umí o svém skriptu říct, ne podle délky skriptu.

## Zdroje
- Všechny moduly D1–D3 — capstone nepřináší novou látku, skládá existující.
