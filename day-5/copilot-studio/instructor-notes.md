# Instructor notes — Copilot Studio

## Timing

- 40 min výklad + 50 min lab. Největší hands-on týdne — výklad nekynout, DLP a kredity stačí po 5 minutách s odkazem na README.

## Go/no-go — otestovat před během

- **Přístup studentů do Copilot Studia**: PAYG billing policy / trial pro 20 účtů — ověřit přihlášení testovacím studentským účtem vč. založení agenta a SharePoint knowledge. No-go → fallback (stavba podle návrhů studentů na projektoru).
- **Environment**: studenti staví v `KUZK-lab` (Sandbox — založit před během; postup viz GOC224 `day-3/power-automate-invoices/explainer-environments.md`), role Environment Maker, billing policy připojená k tomuto environmentu. Ověřit, že se Copilot Studio otvírá v něm, ne v defaultu (přepínač environmentu vpravo nahoře).
- Ověřit, že **Restricted SharePoint Search není zapnutý** (blokoval by SharePoint knowledge celé třídě).
- DLP politiky prostředí: nesmí blokovat SharePoint knowledge, ale ideálně mít **zablokovaný HTTP node a neautentizovaný chat** — governance příklad k ukázání.

## Tripwires

- „Message packs" neexistují — **Copilot Credits** (od 9/2025). Studenti s dřívější zkušeností se budou ptát.
- Test hranice práv (krok 4) je hvězda labu — nechat každou dvojici říct výsledek nahlas; váže celý týden (permissions > licence > scope).
- Hlídat publikaci — krok 6 je záměrně zákaz; kdo publikuje, dělá to přes Requests flow jako demo pro všechny.
- Kreditová disciplína: 20 studentů × ladění = reálné peníze; budget alert (viz environment.md) pořád běží.

## Vazby

- Zpět: návrhy a evaluační plány (včerejší copilot-agents, D4), popisy/instrukce (prompting, D4); DLP a registry se vrací odpoledne v copilot-admin (D5). Skills a RSS/RCD jsou mimo tento běh (plný kurz GOC224).
- Dopředu: capstone — agent je jedna sekce blueprintu, evaluační plán a governance flow se recyklují.
