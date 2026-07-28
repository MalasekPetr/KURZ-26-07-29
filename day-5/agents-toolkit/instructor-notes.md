# Instructor notes — Microsoft 365 Agents Toolkit

## Go/no-go

- VS Code + rozšíření Agents Toolkit připravené; M365 login. `Provision`/běh **empiricky fungují i pro studenty na PAYG** (ověřeno 2026-07-17; MS nedokumentuje) — může každý sám, nebo společný instruktorský běh. Re-verify před během.
- Knihovna `Runbooky` na `/sites/hr-demo` (z `New-HRAgentData.ps1 -Reseed`).
- Když čas tlačí: lab zkrátit na scaffold + editaci manifestu (část A+B), běh nechat jako jeden instruktorský run.

## Tripwires

- **Publikum jsou správci, ne vývojáři** — držet na úrovni „agent jako konfigurace" (JSON), ne kódování. Akci NEstavět (mimo rozsah, jen M365).
- Nosná pointa: **agent jako kód** (git, provisioning, publikace přes schválení) vs. Agent Builder = osobní klikačka bez source control.
- Když padne dotaz „proč Toolkit nedělá HR list": `list_id` = knihovna, ne list (schema 1.7) — tabulková data jen přes Dataverse/konektor/akci. Není slabý, je to špatný nástroj na tuhle práci.

## Vazby

- Zpět: Agent Builder a SharePoint agents (předchozí bloky), srovnání ([`../copilot-agents/comparison-agent-paths.md`](../../day-4/copilot-agents/comparison-agent-paths.md)).
- Dopředu: správa agentů → D5 `copilot-admin`; analytika/low-code → D5 Copilot Studio; repo-as-code přístup → capstone.
