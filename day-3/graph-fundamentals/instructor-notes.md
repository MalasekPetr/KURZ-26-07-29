# Instructor notes — Microsoft Graph prakticky

## Timing

- 45 min výklad + 60 min Lab 4. Výklad vést od Graph Exploreru (navazuje na demo z D1,
  teď si sáhnou sami) — batch/delta/throttling jen jako výhledový závěr, ne hloubka.

## Go/no-go — KLÍČOVÉ, otestovat před během

- Ověřit, že kurzový tenant má dostatek objektů (uživatelé/weby), aby dotaz bez `$top`
  omezení reálně vyžadoval víc než jednu stránku — pokud ne, doplnit demo data předem.
- Připravit simulovaný 429 response log (soubor se sekvencí odpovědí včetně `Retry-After`
  hlavičky) — krok 4 labu na něm stojí; reálné vyvolání throttlingu je jen bonus.
- Graph Explorer s admin consentem pro `Sites.Read.All` na kurzovním tenantu (stejná
  příprava jako demo v D1).

## Tripwires

- Účastníci často implementují pevný `Start-Sleep -Seconds N` místo čtení `Retry-After` z
  odpovědi — v ověření labu explicitně kontrolovat, že čtou hlavičku, ne hardcoded konstantu.
- Krok 2 (skript bez smyčky „tiše lže") nechat účastníky **zažít**, ne jen říct — moment,
  kdy počet výsledků nesedí s Explorerem, je nejcennější učební bod labu.
- `$filter` syntaxe (OData) je pro začátečníky neintuitivní (`eq`, uvozovky) — nechat
  skládat v Exploreru, kde chybu vrátí hned; do skriptu přenášet už ověřený dotaz.
- Nechodit do hloubky batch/delta/change notifications — výhled je jedna stránka výkladu;
  hloubka je v mateřském kurzu GOC223 (backlog).

## Vazby

- Dopředu: `Get-AllGraphResults` a klasifikace chyb se přímo použijí
  v [`../capstone-mini/`](../capstone-mini/) (Lab 5).
- Zpět: navazuje na Graph Explorer demo z [`../../day-1/api-landscape/`](../../day-1/api-landscape/),
  JSON čtení z [`../../day-1/formats-fundamentals/`](../../day-1/formats-fundamentals/)
  a `Connect-CourseTarget` wrapper a auth módy z [`../../day-2/powershell-deep-dive/`](../../day-2/powershell-deep-dive/).
