# Lab 1 · První skript: JSON → PowerShell → Copilot Chat

> Odhad: 60 min · Režim: lokální (bez připojení k tenantu, bez autentizace)

## Cíl

Účastník má vlastní PowerShell skript vygenerovaný s pomocí M365 Copilot Chat, kterému
rozumí řádek po řádku, otestoval ho nad JSON vstupem a upravil jeho chování. Žádné
API se zatím nevolá — autentizace přijde v D2.

## Předpoklady

- VS Code + PowerShell extension (PowerShell 7).
- Přihlášený M365 Copilot Chat (kurzovní účet, ikona štítu = firemní ochrana dat).
- Vstupní soubor `sites.json` od instruktora (fiktivní seznam webů: název, vlastník,
  počet dokumentů, poslední aktivita).

## Kroky

1. **JSON ručně**: otevřít `sites.json` ve VS Code, přidat do něj vlastní web jako nový
   objekt v poli (dodržet čárky a uvozovky — editor chyby podtrhne).
2. **Pipeline interaktivně** (konzole, ne skript):
   `Get-Content sites.json | ConvertFrom-Json` → uložit do `$sites` → `$sites | Get-Member`
   → `$sites | Where-Object docCount -gt 100 | Sort-Object lastActivity`.
3. **Prompt pro Copilot Chat** — česky, s akceptačními kritérii, např.:
   *„Napiš PowerShell 7 skript. Vstup: cesta k JSON souboru se seznamem webů (pole
   objektů s poli name, owner, docCount, lastActivity). Výstup: tabulka webů bez
   aktivity za posledních 180 dní, seřazená od nejstarší. Skript má mít param() pro
   cestu a počet dní, ošetřit neexistující soubor přes try/catch a nesmí nic mazat
   ani měnit."*
4. **Číst před spuštěním**: vygenerovaný skript vložit do VS Code a projít řádek po
   řádku; ke každému nejasnému řádku se v Copilot Chat zeptat „vysvětli, co dělá tento
   řádek" — dokud nezbude žádný nejasný.
5. **Otestovat**: spustit nad `sites.json`; pak schválně rozbít vstup (smazat čárku,
   zadat neexistující cestu) a ověřit, že `try/catch` odpoví srozumitelně.
6. **Upravit bez Copilotu**: ručně změnit výchozí počet dní v `param()` a přidat do
   výstupu sloupec `owner` — ověření, že skriptu skutečně rozumím.

## Ověření

- [ ] `sites.json` obsahuje účastníkův přidaný web a projde `ConvertFrom-Json` bez chyby.
- [ ] Skript má `param()` s výchozími hodnotami a `try/catch` na čtení souboru.
- [ ] Účastník umí u libovolného řádku skriptu říct, co dělá (namátková kontrola
  instruktorem).
- [ ] Ruční úprava (krok 6) funguje — výstup obsahuje sloupec `owner`.

## Fallback

Pokud Copilot Chat není na účtu dostupný, instruktor promítne svůj chat a účastníci
pracují s předpřipraveným vygenerovaným skriptem (`first-script-sample.ps1` ze sdílené
složky) — kroky 4–6 se nemění. Nedostupnost nahlásit, jde o go/no-go podmínku
z `instructor-notes.md`.
