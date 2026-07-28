# Instructor notes — Copilot admin

## Timing

- 45 min výklad + 30 min lab. Tohle je modul, kvůli kterému správci na kurz přišli — nespěchat, mapa vrstev musí sednout.

## Před během

- [ ] Proklikat živý tenant: CCS taby, Agents sekce, Overview dashboard — **UI se mění po měsících**, slidy vs. realita.
- [ ] Mít v tenantu aspoň 2–3 agenty (z D4 `copilot-agents` / testovací), ať registr není prázdný a je co blokovat.
- [ ] Ověřit, co přesně Global Reader vidí v sekci Agents (preview oblasti se mění) — určuje rozsah labu.
- [ ] Testovací agent na blokaci (demo) — ne ten, který potřebují pozdější bloky D5 (Skills/Studio)!

## Tripwires

- **AI Administrator** role — zdůraznit jako novinku least privilege (správa Copilotu bez Global Admina); ladí s celokurzovým role modelem.
- Researcher/Analyst neřídí agent settings — častý omyl.
- Agent 365: licencuje se **uživatel**, ne agent (glosář) — čekat dotaz „kolik stojí agent".
- Nezabřednout do Purview auditu — to je další blok; tady jen říct „usage ≠ audit".

## Vazby

- Zpět: SAM a archiv/RCD jsou mimo tento běh (plný kurz GOC224) — v mapě vrstev je zmínit jen jako existující vrstvu; SharePoint vrstvu tu zastupuje security hardening (D3).
- Dopředu: audit → `monitoring` (hned potom); 3rd-party řádek mapy → Orchestry (mimo tento běh, plný kurz GOC224); registr a publikace → `copilot-agents` (**včera, D4** — Agent Store flow).
