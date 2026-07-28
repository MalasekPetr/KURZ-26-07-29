# Microsoft 365 Agents Toolkit

> Typ: povinný · Den: 5 · Odhad: krátký blok — **studenti hands-on, společně** · Publikum: **správci**
> Prostředí: viz [`../../environment.md`](../../environment.md) · Názvosloví: [`../../GLOSSARY.md`](../../GLOSSARY.md)
> Kontext: deklarativní agent, mapa cest a srovnání → [`../copilot-agents/`](../../day-4/copilot-agents/README.md)

## Co to je

**Microsoft 365 Agents Toolkit** (nástupce Teams Toolkitu; VS Code / Visual Studio) scaffolduje deklarativního agenta **jako kód** — manifest `declarativeAgent.json`. Repo-as-code: git, review, CI/CD, provisioning. Tvorba zdarma.

## Rámec kurzu — správci, ne vývojáři, jen M365

Učíme ho jako **„agent jako spravovaná konfigurace"** — governance a ALM (manifest v gitu, opakovatelný `Provision`, publikace přes schválení), **ne jako vývoj**. **Akce / API plugin / Azure jsou mimo rozsah** — v labu jen pojmenujeme slot `actions`; „udělej něco v tenantu" = Power Automate (D3).

## Knowledge — fakta z manifestu (schema 1.7)

- SharePoint knowledge = **weby / knihovny / složky / soubory**; `list_id` = **knihovna** (ne list), `unique_id` = soubor/složka.
- **Strukturovaný list manifest jako knowledge nezná** — tabulková data jen přes `Dataverse` / Copilot konektor / akci. Proto Toolkit nedělá HR list; jeho scénář je čtení runbooků.
- Manifest-only funkce (v žádném UI): `editorial_answers` (až 300 Q&A), `behavior_overrides.special_instructions.discourage_model_knowledge`, `user_overrides`.

## Lab

[`lab-toolkit-agent.md`](lab-toolkit-agent.md) — první agent jako spravovaná konfigurace nad knihovnou `Runbooky`. Scénář: [`../copilot-agents/scenario-support-agent.md`](../../day-4/copilot-agents/scenario-support-agent.md).

## Zdroje (Microsoft)

[Add knowledge sources (Agents Toolkit)](https://learn.microsoft.com/en-us/microsoft-365-copilot/extensibility/build-declarative-agents-add-knowledge) · [Declarative agent manifest 1.7](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/declarative-agent-manifest-1.7)

## Stav produktu / delta

> [!WARNING] Ověřit k datu běhu — stav k 2026-07.
> Toolkit 6.0; názvy šablon se mění (stavíme „Declarative Agent" bez akce). Manifest schema pro listy k 2026-07 neexistuje — `list_id` je stále knihovna. **Provisioning i běh deklarativního agenta empiricky fungují i pro studenty na PAYG bez Copilot licence** (ověřeno na kurzu 2026-07-17; MS to takto nedokumentuje — re-verify při GA).
