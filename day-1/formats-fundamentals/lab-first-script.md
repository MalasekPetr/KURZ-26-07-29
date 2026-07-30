# Lab 1 · První skript: JSON → PowerShell → Copilot Chat

> Odhad: 60 min · Režim: lokální (bez připojení k tenantu, bez autentizace)

## Cíl

Účastník má vlastní PowerShell skript vygenerovaný s pomocí M365 Copilot Chat, kterému
rozumí řádek po řádku, otestoval ho nad JSON vstupem a upravil jeho chování. Žádné
API se zatím nevolá — autentizace přijde v D2.

## Předpoklady

- VS Code + PowerShell extension (PowerShell 7).
- Přihlášený M365 Copilot Chat (kurzovní účet, ikona štítu = firemní ochrana dat).
- Vstupní soubor [`sites.json`](sites.json) (součást tohoto modulu — 15 fiktivních
  webů: `name`, `owner`, `docCount`, `lastActivity`; 6 z nich je bez aktivity přes
  180 dní, ať má filtrování co najít).

## Kroky

1. **JSON ručně**: otevřít `sites.json` ve VS Code, přidat do něj vlastní web jako nový
   objekt v poli (dodržet čárky a uvozovky — editor chyby podtrhne). Do názvu webu
   a vlastníka napište **plnou českou diakritiku** („Účtárna — Šárka Řeháčková") a
   zkontrolujte ve stavovém řádku VS Code, že soubor je **UTF-8** — diakritiku
   poneseme celým labem.
2. **Pipeline interaktivně** (konzole, ne skript):
   `Get-Content sites.json | ConvertFrom-Json` → uložit do `$sites` → `$sites | Get-Member`
   → `$sites | Where-Object docCount -gt 100 | Sort-Object lastActivity`.
3. **Priming prompt proti fantazii**: otevřít novou konverzaci v Copilot Chat a jako
   první zprávu vložit **verzi 1** z [`copilot-priming-prompt.md`](copilot-priming-prompt.md).
   Pak otestovat návnadou (Test A — neexistující cmdlet, postup tamtéž) a sledovat,
   jak model reaguje. (Test B a verze 2 přijdou v D2, až se poprvé připojíte
   k tenantu.) Teprve potom zadat úlohu — česky, s akceptačními kritérii, např.:
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
7. **UTF-8 round-trip do Excelu**: výsledek uložit
   `... | Export-Csv report.csv -Encoding utf8BOM -UseCulture -NoTypeInformation`
   a otevřít v Excelu — „Šárka Řeháčková" musí být čitelná. Pak schválně exportovat
   podruhé **bez** `-Encoding utf8BOM` a porovnat („NovÃ¡kovÃ¡" efekt) — ať vidíte,
   proč se kódování píše explicitně.

## Ověření

- [ ] Konverzace v Copilot Chat začíná priming promptem a účastník viděl reakci na
  návnadu s neexistujícím cmdletem.
- [ ] `sites.json` obsahuje účastníkův přidaný web a projde `ConvertFrom-Json` bez chyby.
- [ ] Skript má `param()` s výchozími hodnotami a `try/catch` na čtení souboru.
- [ ] Účastník umí u libovolného řádku skriptu říct, co dělá (namátková kontrola
  instruktorem).
- [ ] Ruční úprava (krok 6) funguje — výstup obsahuje sloupec `owner`.
- [ ] Česká diakritika přežila celý round-trip: JSON → pipeline → CSV → Excel
  (krok 7, s `utf8BOM`).

## Fallback

Pokud Copilot Chat není na účtu dostupný, instruktor promítne svůj chat a účastníci
pracují s předpřipraveným vygenerovaným skriptem (`first-script-sample.ps1` ze sdílené
složky) — kroky 4–6 se nemění. Nedostupnost nahlaste lektorovi.
