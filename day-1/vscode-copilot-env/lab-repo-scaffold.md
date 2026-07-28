# Lab · Inicializace repo, scaffolding, linting & testy

> Odhad: 45 min · Režim: simulace

## Cíl

Účastník má funkční repozitář s VS Code workspace konfigurací (tasks pro lint/test) a
skript z Labu 1 v něm prošel celým workflow: Copilot Chat návrh → čtení → lint/test →
commit s popisnou zprávou.

## Předpoklady

- VS Code + PowerShell extension nainstalované; PowerShell 7.
- Přihlášený M365 Copilot Chat (kurzovní účet) v prohlížeči.
- Git nainstalovaný, `user.name`/`user.email` nastavené.
- Hotový skript z Labu 1 ([`../formats-fundamentals/lab-first-script.md`](../formats-fundamentals/lab-first-script.md)).

## Kroky

1. Vytvořit nový lokální repozitář (`git init`) se strukturou `scripts/`, `tests/`;
   skript z Labu 1 přesunout do `scripts/`.
2. Založit `.vscode/tasks.json` se tasky `lint` (PSScriptAnalyzer) a `test` (Pester) —
   kostru souboru nechat vygenerovat Copilot Chatem a před uložením přečíst (je to
   JSON z dopoledního bloku).
3. Nechat si od Copilot Chatu vygenerovat jednoduchý Pester test pro skript z Labu 1
   (prompt s kritérii: co má test ověřit — např. že skript vyhodí chybu na neexistující
   cestu). Test přečíst a vysvětlit.
4. Spustit `lint` a `test` task, opravit nálezy (typicky PSScriptAnalyzer warnings ve
   vygenerovaném kódu — učební moment: i AI kód má nálezy).
5. Self-review podle checklistu z `README.md` (žádné hardcoded identifikátory, error
   handling, `-WhatIf` u destruktivních operací) — nálezy zapsat do commit zprávy.
6. Dva commity s popisnou zprávou (PROČ, ne jen CO): skript + konfigurace, testy.

## Ověření

- [ ] `.vscode/tasks.json` obsahuje minimálně `lint` a `test` task a oba proběhnou bez chyby.
- [ ] Repozitář obsahuje alespoň jeden Pester test, který prochází.
- [ ] Commit historie obsahuje alespoň 2 malé commity s popisnou zprávou (ne jeden velký commit).
- [ ] Účastník potvrdil checklistem, že v kódu nejsou hardcoded identifikátory.

## Fallback

Pokud instalace PSScriptAnalyzer/Pester selže kvůli síťovým omezením učebny, instruktor
poskytne předpřipravený repozitář se závislostmi nainstalovanými offline (USB/sdílená
složka) a lab pokračuje od kroku 2. Pokud není dostupný Copilot Chat, platí fallback
z Labu 1 (předpřipravené soubory, workflow čtení/test se nemění).
