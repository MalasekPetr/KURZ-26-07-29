# Mini-capstone: vlastní skript s Copilot Chat

> Typ: povinný · Den: 3 (závěr automatizační části) · Odhad: 10 min zadání + 60 min Lab 5 + 20 min prezentace & next steps

## Cíle
- Samostatně projít celý workflow kurzu bez vodítek: úloha → prompt s akceptačními
  kritérii → čtení návrhu → test → běh nad živým tenantem pod správnou identitou.
- Prokázat, že tři dny drží pohromadě: API mapa (D1), identita a certifikát (D2),
  Graph dotaz se stránkováním (D3).
- Odnést si osobní „first automation" plán: kterou reálnou úlohu z vlastní firmy
  zautomatizuji jako první a co k tomu potřebuji.

## Průběh

### Zadání (10 min)
Každý účastník si vybere jednu úlohu (nebo přinese vlastní — vítáno):
1. **Inventura webů** — seznam webů tenantu s vlastníkem a poslední aktivitou, výstup
   JSON + čitelná tabulka; označit weby bez aktivity za N dní.
2. **Report členství** — pro zadaný web vypsat, kdo má jaký přístup (vlastníci/členové/
   návštěvníci), výstup pro vedoucího, který chce vědět „kdo vidí co".
3. **Doplnění metadat** — nad pracovním webem `-dev` dávkově založit/aktualizovat
   položky seznamu z JSON vstupu (jediná zapisující úloha — jen nad vlastním webem,
   s `-WhatIf` režimem). Vstupní JSON si vygenerujte Copilotem dle
   [`../../day-1/formats-fundamentals/guide-dummy-data.md`](../../day-1/formats-fundamentals/guide-dummy-data.md).
4. **Audit rozšíření tenantu** — report toho, co v tenantu běží: nasazená SPFx řešení
   (`Get-PnPApp -Scope Tenant`), schválené API access granty a zaregistrované site
   templates; výstup jako CSV pro správce (vazba na [`../spfx-admin/`](../spfx-admin/)
   a [`../site-list-templates/`](../site-list-templates/)).
5. **Vlastní šablona jako kód** — vygenerovat site script z vlastního `-dev` webu,
   upravit ho a zdokumentovat jako opakovatelnou šablonu pro vaši organizaci
   (výstup: JSON v repu + README, jak ho použít).

Všechny úlohy běží pod app registrací z D2 (delegated, případně app-only cert pro
čtecí varianty) nad pracovními weby z Labu 3.

### Lab (60 min)
Viz [`lab-capstone-script.md`](lab-capstone-script.md).

### Prezentace & next steps (20 min)
Dva dobrovolníci ukáží skript a hlavně **prompt a proces** (co Copilot navrhl špatně
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
