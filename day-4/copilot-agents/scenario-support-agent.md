# Scénář · Support Asistent — agent jako spravovaná konfigurace (Agents Toolkit)

> Modul: copilot-agents · Typ: referenční scénář pro Toolkit lab · Publikum: **správci, ne vývojáři**
> Srovnání cest: [`comparison-agent-paths.md`](comparison-agent-paths.md) · Datový protějšek: [`scenario-hr-agent.md`](scenario-hr-agent.md)

Druhý scénář kurzu ukazuje Agents Toolkit **z pohledu správce** — ne jako vývoj, ale jako **agent jako verzovaná, řízená konfigurace**. Vše zůstává **v M365**: žádné Azure, žádné externí API, žádné psaní kódu — jen editace **manifestu (JSON)** a provisioning do tenantu.

> [!IMPORTANT] Rozsah kurzu
> Studenti jsou **správci**. Držíme se **uvnitř M365**. **Akce / API plugin** (agent volá externí API) je developerská nadstavba a **je mimo rozsah** — v labu ji jen pojmenujeme (slot `actions` v manifestu), nestavíme ji. Kdo chce agenta „který něco udělá" v tenantu, jde přes Power Automate (D3), ne přes vlastní API.

## Persona a účel

**Support Asistent** odpovídá supportnímu týmu z **provozních runbooků** (KB, postupy). Čte, negrounduje relačně, nic nezapisuje.

- *„Jak vyřešit ‚access denied' při uploadu do knihovny?"*
- *„Jaká je SLA na incident P1?"*
- *„Jaký je postup při výpadku tiskové fronty?"*

Knowledge = dokumentová knihovna `Runbooky` na `/sites/hr-demo` (soubory — Toolkit knihovny scopuje bez problému; `list_id` = knihovna).

## Proč zrovna Toolkit (pro správce)

Tentýž typ agenta (čte knowledge) postavíš i v Agent Builderu za minuty. Rozdíl, který učíme, **není schopnost, ale disciplína stavby**:

| Hledisko | Agent Builder | **Agents Toolkit** |
| --- | --- | --- |
| Kde agent vzniká | klikací nástroj v Copilot appce | **manifest (JSON) v repu** |
| Source control | žádný | **git — diff, review, historie** |
| Nasazení | ruční | **`Provision` (opakovatelné, přes environmenty)** |
| Publikace | sdílení / katalog | katalog / marketplace **přes schválení** |
| Governance / ALM | osobní nástroj | **řízený životní cyklus** |

Pro správce je nosná hodnota právě **governance a lifecycle** — „agent jako konfigurace, kterou verzuješ, revleš a nasazuješ", ne osobní klikačka. To je celé v M365.

## Co v manifestu ukážeme (bez kódu)

- **`instructions`** — chování agenta (D4: purpose → guidelines). Text, ne kód.
- **`capabilities` → `OneDriveAndSharePoint`** — knowledge scopnutá na knihovnu `Runbooky` (`items_by_url`).
- **`editorial_answers`** — až 300 předdefinovaných Q&A (např. SLA), manifest-only funkce (v žádném UI není).
- **`behavior_overrides.special_instructions.discourage_model_knowledge`** — „drž se zdrojů".
- **slot `actions`** — jen ukázat a pojmenovat: „sem by šlo zapojit API; to je vývoj, mimo náš rozsah".

## Data a setup

Knihovna `Runbooky` (4 .md runbooky) vzniká spolu s HR daty přes `scripts/New-HRAgentData.ps1` (zdroj: `scripts/runbooks/*.md`) — viz [`scenario-hr-agent.md`](scenario-hr-agent.md#provisioning). **Žádné Azure, žádné API, žádný hosting.**

## Evaluační plán (4 testy)

| # | Dotaz | Co testuje | Očekávání |
| --- | --- | --- | --- |
| 1 | „Jak vyřešit ‚access denied' při uploadu?" | knowledge — runbook | odpověď ze souboru |
| 2 | „Jaká je SLA na P1?" | editorial answer | předdefinovaná odpověď |
| 3 | „Postup při výpadku tiskové fronty?" | knowledge — runbook | ze souboru |
| 4 | „Napiš básničku o počasí." | `discourage_model_knowledge` | odmítne / zůstane u zdrojů |

## V čem se liší od HR scénáře

- HR: číst **strukturovaný list** → Agent Builder (analytika = Copilot Studio v D5).
- Support: číst **knihovnu runbooků** → Agent Builder by to taky uměl; smysl Toolkitu je **ukázat stavbu jako řízenou konfiguraci** (git, provisioning, publikace přes schválení).
- Lekce pro správce: **stejný agent, jiná disciplína** — governance a lifecycle, ne klikačka.

## Stav produktu / delta

> [!WARNING] Ověřit k datu běhu — stav k 2026-07.
> UI Agents Toolkitu (aktuálně 6.0) a názvy šablon se mění — ověřit „Declarative Agent" bez akce. `editorial_answers`/`discourage_model_knowledge` = manifest schema 1.8 (dřív 1.7, funkce beze změny). Provisioning/běh deklarativního agenta: MS v manifestu 1.8 dokumentuje **metered usage (PAYG) NEBO Copilot licenci** — na PAYG tenantu tedy funguje i studentům.
