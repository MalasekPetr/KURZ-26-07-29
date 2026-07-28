# Instructor notes — Mini-capstone

## Timing

- 15 min zadání + 75 min Lab 5 + 30 min prezentace & next steps. Elastický blok:
  při skluzu dne zkrátit prezentace (1 dobrovolník + kolečko „first automation"),
  lab nekrátit — je to hlavní důkaz hodnoty kurzu.

## Go/no-go

- Ověřit, že úlohy 1–3 jsou splnitelné s permissions, které účastníkům zbyly po
  hardening auditu (D2) — projít den předem na testovacím účtu; úloha 3 potřebuje
  zapisovací permission nad vlastním webem (delegated `Sites.ReadWrite.All`, nebo
  ponechané `Sites.FullControl.All` application — zdůvodnění je součást úlohy).
- Připravit vstupní JSON pro úlohu 3 (šablona položek) do sdílené složky.

## Tripwires

- Krok 1 (rozmyslet před promptem) nesmí odpadnout — bez něj se lab zvrhne
  v prompt-roulette; vymáhat papír/poznámku před otevřením chatu.
- Hlídat scope zápisu: úloha 3 výhradně nad vlastním `-dev` webem; jakýkoli zápis
  jinam okamžitě stopnout (25 GA v jednom tenantu — pravidla z D1).
- Slabší účastníci ať volí úlohu 1 (nejpřímější mapování na Lab 4); silnější úlohu 3.
- Prezentace držet u **procesu** (prompt → chyba → odhalení), ne u předvádění kódu —
  to je sdělení, které si firma má odnést: chyby AI návrhů se dají systematicky chytat.
- „First automation" kolečko zapsat (flipchart/foto) — je to podklad pro follow-up
  a případný navazující běh.

## Vazby

- Zpět: skládá všechny bloky D1–D3; hodnotící checklist = ověření Labů 1–4.
- Dopředu: „first automation" seznam + hardening checklist se propíší do capstone
  blueprintu D5 (rollout automatizace + AI dohromady).
