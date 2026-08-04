# Microsoft 365 Agents Toolkit

> Typ: povinný · Den: 5 · Odhad: krátký blok — **studenti hands-on, společně** · Publikum: **správci**
> Prostředí: viz [`../../environment.md`](../../environment.md) · Názvosloví: [`../../GLOSSARY.md`](../../GLOSSARY.md)
> Kontext: deklarativní agent, mapa cest a srovnání → [`../copilot-agents/`](../../day-4/copilot-agents/README.md)

## Co to je

**Microsoft 365 Agents Toolkit** (nástupce Teams Toolkitu; VS Code / Visual Studio) scaffolduje deklarativního agenta **jako kód** — manifest `declarativeAgent.json`. Repo-as-code: git, review, CI/CD, provisioning. Tvorba zdarma.

## Rámec kurzu — správci, ne vývojáři, jen M365

Učíme ho jako **„agent jako spravovaná konfigurace"** — governance a ALM (manifest v gitu, opakovatelný `Provision`, publikace přes schválení), **ne jako vývoj**. **Akce / API plugin / Azure jsou mimo rozsah** — v labu jen pojmenujeme slot `actions`; „udělej něco v tenantu" = Power Automate (D3).

## Knowledge — fakta z manifestu (schema **1.8**)

- SharePoint knowledge = **weby / knihovny / složky / soubory**; `list_id` = **knihovna dokumentů** (ne list), `unique_id` = soubor/složka. Platí i v 1.8 — dokumentace `list_id` popisuje výslovně jako *document library*.
- **Strukturovaný list manifest jako knowledge nezná** — tabulková data jen přes `Dataverse` / Copilot konektor / akci. Proto Toolkit nedělá HR list; jeho scénář je čtení runbooků.
- Manifest-only funkce (v žádném UI): `editorial_answers` (až 300 Q&A), `behavior_overrides.special_instructions.discourage_model_knowledge`, `user_overrides`, `disclaimer`, `default_response_mode` (`Auto` / `Quick response` / `Think deeper`).
- `EmbeddedKnowledge` (soubory přímo v balíčku, max 1 MB / 10 souborů) je popsané, ale **dosud neaktivované** — pozná se i podle `sensitivity_label`, které je na embedded soubory vázané.
- `worker_agents` (agent volá jiného agenta) = **preview**; referencuje se přes `id` nebo `file`.

## Co přineslo schema 1.8 — a co ne

Oproti 1.7 přidalo 1.8 **dvě zápisové capability**, ne podporu listů, na kterou jsme čekali:

| Capability | Co umí | Proč to zajímá správce |
|---|---|---|
| `EmailActions` | zápis do pošty — triage (archivace, příznaky, přesun), **supervised send**, mazání, pravidla schránky, auto-reply, správa složek | agent přestává být „jen čtenář"; scope se **neřídí** omezeními read-only capability `Email` (`folders`, `shared_mailbox`) — deklaruje se samostatně |
| `MeetingActions` | plánování schůzek, ankety na hledání času, time insights | zápis do kalendáře; sada akcí se bude vyvíjet |

**Governance důsledek pro schvalování agentů**: u agenta s `EmailActions`/`MeetingActions` už neschvalujete „co agent uvidí", ale **co agent smí udělat jménem uživatele**. Do checklistu pro schválení tedy patří otázka „obsahuje manifest zápisovou capability?" — a pokud ano, kdo to schválil a proč. Strukturované listy jako knowledge v 1.8 **nepřišly**; kdo je potřebuje, zůstává u Copilot Studia nebo SharePoint agenta ([`../../day-4/copilot-agents/comparison-agent-paths.md`](../../day-4/copilot-agents/comparison-agent-paths.md)).

## Lab

[`lab-toolkit-agent.md`](lab-toolkit-agent.md) — první agent jako spravovaná konfigurace nad knihovnou `Runbooky`. Scénář: [`../copilot-agents/scenario-support-agent.md`](../../day-4/copilot-agents/scenario-support-agent.md).

## Zdroje (Microsoft)

[Add knowledge sources (Agents Toolkit)](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/build-declarative-agents-add-knowledge) · [Declarative agent manifest **1.8**](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/declarative-agent-manifest-1.8) · [JSON schema v1.8](https://developer.microsoft.com/json-schemas/copilot/declarative-agent/v1.8/schema.json) · [Connect to other agents (worker agents)](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/declarative-agent-connected-agent)

## Stav produktu / delta

> [!IMPORTANT] Aktualizováno po běhu — schema 1.8 vyšlo.
> `version` v manifestu = `v1.8`. Novinky = `EmailActions` a `MeetingActions` (viz výše), **ne** podpora strukturovaných listů. Materiály kurzu se odkazovaly na 1.7; odkazy i tvrzení jsou přepsané na 1.8.

> [!IMPORTANT] Licencování je teď dokumentované.
> Microsoft v manifestu 1.8 výslovně uvádí: agenta s jinou capability než `WebSearch` může uživatel používat, **pokud tenant povoluje metered usage (PAYG), NEBO má uživatel M365 Copilot licenci**. Empirické zjištění z běhu (Toolkit funguje i na PAYG bez licence) tedy odpovídá dokumentaci — už to není „docs lag".

> [!WARNING] Ověřit k datu příštího běhu.
> Toolkit 6.0; názvy šablon se mění (stavíme „Declarative Agent" bez akce). `EmbeddedKnowledge` a `sensitivity_label` sledovat — až se aktivují, změní se scénář „malé znalostní soubory bez SharePointu".
