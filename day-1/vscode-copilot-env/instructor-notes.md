# Instructor notes — VS Code + Copilot Chat workflow

## Timing

- 45 min výklad + 45 min lab. Poslední blok D1 — je to elastický ventil dne: při skluzu
  (typicky MFA onboarding) se lab dokončí ráno D2, výklad ale musí proběhnout v D1.

## Go/no-go — KLÍČOVÉ, otestovat před během

- M365 Copilot Chat free se otevře pod **studentským** účtem `cloudedu.cz` a ukazuje
  enterprise data protection (ikona štítu) — test den před během; sdílená go/no-go
  podmínka s Labem 1 (`../formats-fundamentals/instructor-notes.md`).
- Ověřit dostupnost PowerShell Gallery z učebny (firewall) — instalace
  PSScriptAnalyzer/Pester vyžaduje síť; jinak připravit offline balíčky.

## Tripwires

- Účastníci mají tendenci akceptovat první Copilot návrh bez čtení — trvat na nahlas
  vysloveném review před `git commit`; namátkově „co dělá řádek X?".
- Nenechat diskuzi sklouznout k obecné debatě „nahradí nás AI" — cíl bloku je konkrétní
  pracovní návyk (prompt s kritérii → čtení → test), ne filozofie. Pro tuhle firmu je
  ale legitimní otázka „kde končí naše data" — odpověď: enterprise data protection,
  prompty netrénují modely; umět ji říct jistě.
- Git je pro většinu skupiny nová látka — držet se lineárního `main` s malými commity;
  branch/PR jen zmínit jako cílový stav, nedrilovat. Nepředpokládat žádnou Git znalost.
- Copy/paste workflow chat ↔ editor je záměr (free verze nemá editor integraci) —
  nepůsobit omluvně; výhoda: nutí číst kód při přenosu.
- Část skupiny může znát ISE — ukázat "PowerShell: Enable ISE Mode" jako můstek.
  Argument pro přechod: ISE neumí PowerShell 7, kterým kurz jede.

## Vazby

- Dopředu: repo hygiena a review disciplína se vyžaduje po celý zbytek týdne (všechny
  laby produkují kód do stejného repozitáře). Identita a app registrace v
  [`../../day-2/automation-strategy/`](../../day-2/automation-strategy/) navazují.
- Zpět: staví na skriptu a Copilot workflow z [`../formats-fundamentals/`](../formats-fundamentals/)
  a pravidlech z [`../onboarding/`](../onboarding/) (nic citlivého do promptů).
