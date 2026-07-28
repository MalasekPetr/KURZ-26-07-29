# Instructor notes — PowerShell do hloubky

## Timing

- 45 min výklad + 90 min Lab 3 (nosný lab dne 2 — počítat s rezervou;
  instalace tří modulů může zabrat 10-15 minut na pomalejší síti, pustit na pozadí hned
  na začátku bloku). Publikum jsou začátečníci s jedním dnem PowerShellu — smyčku
  a `param()` v krocích 5-6 nechat nejdřív vygenerovat Copilot Chatem (workflow z D1)
  a číst, ne psát na čas.

## Go/no-go — KLÍČOVÉ, otestovat před během

- Ověřit, že app registrace z [`../automation-strategy/`](../automation-strategy/) má `-ClientId` funkční pro PnP interaktivní přihlášení —
  od 9. 9. 2024 PnP.PowerShell vyžaduje vlastní ClientId, sdílené výchozí už nefunguje.
- **Projít celý flow labu na testovacím účtu den předem**: `New-SelfSignedCertificate` na
  učebním stroji (práva k CurrentUser store jsou standard, ale ověřit image učebny), upload
  `.cer` na app registraci, app-only `Connect-PnPOnline -Thumbprint`, a hlavně
  **`New-PnPSite` v app-only režimu** — tenant policy umí app-only vytváření webů blokovat;
  pokud blokuje, aktivovat Fallback variantu (delegated vytvoření) rovnou ve výkladu.
- Admin consent pro `Sites.FullControl.All` si studenti dávají sami (GA) — připomenout
  pravidla z [`../../day-1/onboarding/ways-of-working.md`](../../day-1/onboarding/ways-of-working.md), je to druhá tenant-wide akce dne.
- Pokud v učebně není druhé zařízení pro device code test, mít připravený telefon/tablet
  jako záložní "druhé zařízení" pro demo.

## Tripwires

- Studenti si pletou `-ClientId` aplikace s `-TenantId` — zdůraznit rozdíl hned na začátku.
- **Nenechat nikoho exportovat `.pfx` "pro zálohu"** — celý bod labu je, že private key
  neopouští stroj/store; `.cer` (veřejná část) je jediný soubor, který se přenáší.
- Weby vytvářet skriptem (smyčka přes dev/test/prod), ne 3× ručně v UI — jde o návyk
  parametrizace; UI-cesta neprojde ověřením.
- SPO modul v PowerShell 7 potřebuje `-UseWindowsPowerShell` při importu na některých verzích —
  mít na slidu jako rychlou opravu, pokud `Import-Module` selže.

## Vazby

- Dopředu: čerstvý certifikát dostane hned v dalším bloku širší rámec
  ([`../certificates-and-keys/`](../certificates-and-keys/) — formáty, stores, YubiKey demo);
  rotační cvičení v [`../security-hardening/`](../security-hardening/) na něm staví.
  Weby `-dev/-test/-prod` a `Connect-CourseTarget` wrapper jsou přímý vstup pro
  [`../../day-3/graph-fundamentals/`](../../day-3/graph-fundamentals/) (Lab 4) a
  [`../../day-3/capstone-mini/`](../../day-3/capstone-mini/) (Lab 5).
- Zpět: navazuje na app registraci a auth strategii z [`../automation-strategy/`](../automation-strategy/).
