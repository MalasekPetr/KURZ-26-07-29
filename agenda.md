# Agenda — pořadí bloků

Jediný zdroj pravdy o pořadí modulů. Složky jsou slugy; pořadí drží tato tabulka.

**5 dní · zakázkový běh (revize 2026-07-28).** Dny 1–3 = API a skriptování od základů
(výběr z GOC223 + nové bloky), dny 4–5 = AI a Copilot (výběr z GOC224). P = povinný,
V = volitelný. Žádné vstupní znalosti PowerShellu, Gitu ani API se nepředpokládají —
cíl dnů 1–3 je sebejistá, bezpečná práce s API a skripty, ne migrační inženýrství.

## Den 1 — Proč M365 API: mapa, formáty a první skript

| # | Blok | Slug | Typ |
|---|---|---|---|
| 1 | Onboarding & pravidla práce | `day-1/onboarding` | P |
| 2 | Mapa API M365 & SPO: minulost, současnost a budoucnost *(cvičení Graph Explorer)* | `day-1/api-landscape` | P |
| 3 | Formáty dat (JSON, YAML, XML, CSV), UTF-8 a PowerShell od nuly *(Lab 1: první skript s Copilot Chat)* | `day-1/formats-fundamentals` | P |
| 4 | VS Code, Git a Copilot Chat workflow | `day-1/vscode-copilot-env` | P |

> [!NOTE]
> `api-landscape` odpovídá na otázku „proč je automatizace bezpečná" (data zůstávají
> v tenantu, oprávnění jsou vymezená, vše je auditovatelné) a každý si v něm zavolá
> první Graph dotaz. `formats-fundamentals` pak dá každému účastníkovi první
> vlastnoručně přečtený a otestovaný skript ještě v D1. Lab bloku 4 lze podle času
> dokončit ráno D2.

## Den 2 — Klíče od API: identita, permissions a certifikáty

| # | Blok | Slug | Typ |
|---|---|---|---|
| 1 | Nástrojová mapa & app registrace *(Lab 2: registrace aplikace, delegated vs application)* | `day-2/automation-strategy` | P |
| 2 | PowerShell moduly & autentizace *(Lab 3: certifikát, app-only, pracovní weby + mini-lab: tři podpisy zápisu)* | `day-2/powershell-deep-dive` | P |
| 3 | Certifikáty a klíče v praxi: CER/PEM/PFX, stores, YubiKey *(živé demo)* | `day-2/certificates-and-keys` | P |
| 4 | Security hardening & least privilege | `day-2/security-hardening` | P |

> [!NOTE]
> Osa dne: kdo jsem (app registrace) → čím se prokážu (moduly + auth) → jak credential
> bezpečně uložím (certy, hardware klíče) → jak to celé udržím čisté (hardening).
> YubiKey demo předvádí lektor na fyzických klíčích; hands-on část dne běží nad
> softwarovými certifikáty z Labu 3. Hardening na závěr audituje přesně to, co
> během dne vzniklo.

## Den 3 — Data, šablony a správa rozšíření

| # | Blok | Slug | Typ | Čas |
|---|---|---|---|---|
| 1 | Microsoft Graph prakticky *(Lab 4 + živá dema přístupu k datům)* | `day-3/graph-fundamentals` | P | 45 + 60 |
| 2 | Site templates & list templates *(lab: web → seznam → položky → dokumenty)* | `day-3/site-list-templates` | P | 40 + 45 |
| 3 | Správa SPFx: App Catalog, API access, tenant-wide extensions *(demo)* | `day-3/spfx-admin` | P | 40 + 15 |
| 4 | Azure orientace: subscription, RBAC, kde skript běží (+ kontejnery) | `day-3/azure-orientation` | P | 30 + 15 |
| 5 | Node.js & TypeScript — druhá cesta k API *(demo)* | `day-3/opt-node-typescript` | V | 20 |
| 6 | Orchestry — úvod do 3rd-party governance | `day-3/opt-orchestry-intro` | V | 20 |
| 7 | Mini-capstone: vlastní skript s Copilot Chat *(Lab 5)* | `day-3/capstone-mini` | P | 10 + 60 + 20 |

> [!NOTE]
> **Povinné bloky = ~6 h čistého času** — den je plný a volitelné bloky se
> spouští jen při reálné rezervě (uvolňovací ventil v tomto pořadí: `opt-node-typescript`,
> `opt-orchestry-intro`, pak zkrácení `azure-orientation` na 20 min). Osa dne:
> **čtu data** (Graph + zásobník živých demo dotazů) → **vyrábím prostředí ze šablon**
> (site/list templates) → **spravuji, co v tenantu běží** (SPFx, API access — přímé
> navázání na permissions z D2) → **vím, kde to poběží** (Azure) → **skládám vlastní
> skript** (capstone). Mini-capstone se nekrátí; je to hlavní výstup celé automatizační
> části a zdroj „first automation" plánů.

## Den 4 — AI: základy, Copilot & první agenti

| # | Blok | Slug | Typ |
|---|---|---|---|
| 1 | AI landscape a pozicování Copilotu | `day-4/ai-landscape` | P |
| 2 | Licenční modely a nákladové postoje | `day-4/licensing` | P |
| 3 | Základy promptování a agentní anatomie | `day-4/prompting-fundamentals` | P |
| 4 | Agenti — mapa cest tvorby *(úvod: deklarativní agent, srovnání, návrhový lab)* | `day-4/copilot-agents` | P |
| 5 | Agent Builder *(studenti hands-on)* | `day-4/agent-builder` | P |
| 6 | Základy Document processing for Microsoft 365 *(demo autofill columns)* | `day-4/opt-document-processing` | V |

> [!NOTE] Přechodový den: dopoledne rámec (landscape → licence → prompting), odpoledne
> stavba (mapa cest → Agent Builder hands-on). Prompt literacy z bloku 3 se úročí
> okamžitě v návrhovém labu agentů. Document processing jen při rezervě — je to
> největší kandidát na doplnění v případném navazujícím běhu.

## Den 5 — AI: stavba, správa & rollout

| # | Blok | Slug | Typ |
|---|---|---|---|
| 1 | SharePoint agents *(instruktor demo — limit 1 zdroj)* | `day-5/sharepoint-agents` | P |
| 2 | Microsoft 365 Agents Toolkit *(agent jako spravovaná konfigurace)* | `day-5/agents-toolkit` | P |
| 3 | Copilot Studio — stavba nad SharePointem | `day-5/copilot-studio` | P |
| 4 | Nástroje pro správu Copilotu a agentů (M365 AC: Copilot & Agents, Agent 365) | `day-5/copilot-admin` | P |
| 5 | Provozní monitoring a compliance | `day-5/opt-monitoring` | V |
| 6 | Capstone & next steps *(rollout blueprint — automatizace + AI dohromady)* | `day-5/capstone` | P |

> [!NOTE] Oblouk „postavili jste (D4–D5 dopoledne) — teď to řídíte (copilot-admin) —
> a plánujete rollout (capstone)". Capstone je elastický 60–120 min blok a propojuje
> obě části kurzu: blueprint pokrývá automatizační artefakty z D1–3 i agenty z D4–5.
> Agents Toolkit navíc přirozeně navazuje na repo-as-code návyky z automatizačních dnů.
