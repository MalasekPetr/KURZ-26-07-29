# Provozní skripty kurzu

Automatizace životního cyklu kurzového tenantu: provisioning studentů → laby → offboarding.
Všechny skripty budou idempotentní (bezpečné spustit opakovaně) a podporovat `-WhatIf` (dry-run).

> [!IMPORTANT]
> **Identifikátory.** Repo je public. **Žádný skript nebude mít zabudované identifikátory** —
> tenant GUID, ClientId aplikací, cert thumbprint a Azure subscription ID se budou předávat
> výhradně parametry na příkazové řádce. Nikdy je sem nedoplňujte ani necommitujte výstupy
> s hesly (`student-credentials.csv` je gitignored).

> [!NOTE]
> **Stav.** Toto je scaffold — skripty níže jsou plánované (`[PLÁNOVANÉ]`), plné znění vzniká
> postupně během rozpracování dne, ke kterému se vážou.

## Životní cyklus kurzu (plán)

Cílový tenant: M365 Developer tenant `cloudedu.cz` (viz [`../environment.md`](../environment.md)).
Účty `jmeno.prijmeni@cloudedu.cz` (bez diakritiky, max. 25), licence E5 Developer, role
Global administrator. Jmenný seznam účastníků se skriptům předává jako CSV parametr (UTF-8)
z instruktorského kanálu — nikdy není součástí repa.

| Fáze | Skript | API | Váže se k |
|---|---|---|---|
| 1. Účty studentů (vytvoření/reaktivace, E5 licence, role GA) | `New-CourseStudents.ps1` `[PLÁNOVANÉ]` | Graph | `day-1/onboarding` |
| 2. Studentské weby — **jen fallback** (účastníci si `-dev/-test/-prod` weby vytváří sami v Labu 3) + seedování demo obsahu | `New-CourseStudentSites.ps1` `[PLÁNOVANÉ]` | PnP | `day-2/powershell-deep-dive` |
| 3. Demo data pro Graph lab (dost webů/položek na reálné stránkování) + `sites.json` pro Lab 1 — **vše s českou diakritikou, UTF-8** (Lab 1 na tom staví round-trip test) | `New-CourseSeedData.ps1` `[PLÁNOVANÉ]` | PnP/Graph | `day-1/formats-fundamentals`, `day-3/graph-fundamentals` |
| 4. Instruktorské Azure demo (resource group, Function App s managed identity) | `New-CourseDemoAzureResources.ps1` `[PLÁNOVANÉ]` | Az/ARM | `day-3/azure-orientation` |
| 5. Offboarding — smazání obsahu a artefaktů studentů (weby, app registrace) | `Remove-CourseStudentData.ps1` `[PLÁNOVANÉ]` | Graph + PnP | — |
| 6. Offboarding — Azure demo cleanup | `Remove-CourseDemoAzureResources.ps1` `[PLÁNOVANÉ]` | Az/ARM | — |
| 7. Offboarding — disable sign-in + uvolnění licencí | `Disable-CourseStudents.ps1` `[PLÁNOVANÉ]` | Graph | — |

> [!NOTE]
> App registrace pro laby si účastníci zakládají sami (všichni jsou Global
> administrator — Lab 2 v `day-2/automation-strategy`), samostatný provisioning skript pro ně
> není potřeba. O to důležitější je offboarding fáze 5: posbírat a smazat vše, co účastníci
> pod GA rolí vytvořili (dle naming konvence z `day-1/onboarding/ways-of-working.md`).

Pořadí offboardingu: **nejdřív 5, pak 6, pak 7** — mazání obsahu vyžaduje ještě licencované
účty a existující resource groups.

## Přihlašování — tři režimy (všechny skripty)

1. **Interactive** (default) — browser/WAM popup. Pozor: popup jde do default browseru;
   pokud v něm běží jiná identita než admin cílového tenantu, auth tiše selže.
2. **`-UseDeviceCode`** — kód zadáte v libovolném browseru/profilu. Řeší problém č. 1.
3. **`-CertificateThumbprint` + `-ClientId` + `-TenantId` (GUID)** — app-only, bez
   jakéhokoli promptu. Doporučeno pro dávkové operace (offboarding = 20+ připojení).

Detailní návod na app registraci a přiřazení permissions je součástí `day-2/automation-strategy`
(Lab 2: registrace app & baseline oprávnění); rotaci secret → cert řeší
`day-2/security-hardening`.

## Demo data

`seed-data/*` (vzniká s fází 3) — **výhradně fiktivní data** pro laby. Nikdy sem
nenahrávejte reálná zákaznická/personální data; fiktivní obsah lze generovat Copilotem
dle [`../day-1/formats-fundamentals/guide-dummy-data.md`](../day-1/formats-fundamentals/guide-dummy-data.md).
Migrační seed data původního běhu se přesunula do `backlog/migration-patterns` spolu
s migračním modulem.
