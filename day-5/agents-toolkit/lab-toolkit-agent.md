# Lab · První agent v Agents Toolkitu — agent jako spravovaná konfigurace

> Modul: copilot-agents · Odhad: 35 min · Režim: **studenti hands-on, vedeni instruktorem** · Publikum: **správci**
> Scénář: [`../copilot-agents/scenario-support-agent.md`](../../day-4/copilot-agents/scenario-support-agent.md) · Srovnání cest: [`../copilot-agents/comparison-agent-paths.md`](../../day-4/copilot-agents/comparison-agent-paths.md)

## Cíl

Postavit společně deklarativního agenta v Microsoft 365 Agents Toolkitu **jako verzovanou konfiguraci** — editací **manifestu (JSON)**, bez psaní kódu a **bez opuštění M365**. Vidět governance/ALM rozdíl proti Agent Builderu: agent jako **kód v gitu**, ne osobní klikačka.

> [!IMPORTANT] Rozsah — správci, ne vývojáři
> **Žádné Azure, žádné externí API, žádný hosting.** Editujeme jen manifest (JSON — návaznost na formáty z D1/D2, které správce zná). **Akci (API plugin) nestavíme** — je to developerská nadstavba mimo rozsah; jen ukážeme slot `actions` a řekneme, k čemu je (a že „udělej něco v tenantu" se řeší přes Power Automate z D3).

## Předpoklady

- **VS Code** + rozšíření **Microsoft 365 Agents Toolkit** (tvorba zdarma).
- Přihlášení do M365 v Toolkitu. `Provision` i běh deklarativního agenta **empiricky fungují i pro studenty na PAYG bez Copilot licence** (ověřeno na kurzu 2026-07-17; MS to nedokumentuje — re-verify) → může provisionovat a spustit každý sám.
- Knihovna `Runbooky` na `/sites/hr-demo` (vzniká přes `scripts/New-HRAgentData.ps1`).

> [!NOTE] Kdo co dělá
> **Studenti**: scaffold projektu + editace manifestu (JSON) — hands-on, zdarma, v M365. **`Provision` i živý běh** empiricky fungují i studentům na PAYG (ověřeno 2026-07-17; MS nedokumentuje) — může každý sám, nebo společný instruktorský běh. Cíl je *pochopit agenta jako spravovanou konfiguraci*.

## Kroky

### Část A — scaffold a orientace (všichni)

1. V Toolkitu → **Create a New Agent/App** → **Declarative Agent** → **bez akce** (nevybírej „Add an Action"). Vznikne projekt s `appPackage/declarativeAgent.json` a app manifestem.
2. Otevři `declarativeAgent.json`. Najdi `name`, `description`, `instructions`, `capabilities`, (prázdný) `actions`. Pojmenuj, co je co — je to **konfigurace, ne program**.

### Část B — nastav agenta v manifestu (všichni editují)

3. Uprav `name`, `description`, `instructions` na **Support Asistenta** (viz scénář; D4: purpose → guidelines, **instrukce ≠ knowledge**).
4. Přidej **knowledge**: do `capabilities` vlož `OneDriveAndSharePoint` s `items_by_url` na knihovnu **`Runbooky`** (grounding nad soubory).
5. Přidej **`editorial_answers`** s jedním párem (SLA na P1) a **`behavior_overrides.special_instructions.discourage_model_knowledge: true`**.
6. Ukaž prázdný slot **`actions`**: „sem by se zapojilo API — to je vývoj, mimo náš rozsah; my zůstáváme v M365."

### Část C — provisioning a test (studenti i instruktor)

7. **Provision** (Lifecycle pane) → nasadí agenta do tenantu (žádné Azure). Empiricky funguje i studentům na PAYG (2026-07-17) — každý svůj běh, nebo společný instruktorský.
8. Pusť 4 dotazy scénáře: access-denied runbook, SLA (editorial answer), tisková fronta, a negativní (`discourage_model_knowledge`).
9. Ukaž **manifest v gitu vs. běžící agent**: „tohle je verzovatelné, review-ovatelné, nasaditelné přes schválení" — porovnej s Agent Builderem z dopoledne (žádný source control, osobní nástroj).

## Ověření

- [ ] Projekt scaffoldnutý (bez akce), `declarativeAgent.json` upravený na Support Asistenta.
- [ ] Student umí v manifestu ukázat `instructions`, `knowledge` (Runbooky) a `editorial_answers`.
- [ ] U živého běhu: dotaz 1/3 odpověděly z runbooku, dotaz 4 odmítnut.
- [ ] Formulován rozdíl proti Agent Builderu: **agent jako spravovaná konfigurace** (git, provisioning, publikace přes schválení) — governance/ALM.

## Fallback

- Toolkit/provisioning nedostupný: studenti scaffoldnou a upraví manifest lokálně (zdarma, bez běhu); instruktor promítne přípravný běh nebo screenshoty. Části A a B jsou plnohodnotný deliverable i bez `Provision`.

## Zdroje (Microsoft)

[Add knowledge sources (Agents Toolkit)](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/build-declarative-agents-add-knowledge) · [Declarative agent manifest 1.8](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/declarative-agent-manifest-1.8)
