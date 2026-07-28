# Návod · Demo D4: tři cesty tvorby agentů na dvou scénářích

> Modul: copilot-agents · Typ: instruktorský demo playbook · Režim: živé demo + hands-on studentů (Copilot licence u instruktora)
> Scénáře: [`scenario-hr-agent.md`](scenario-hr-agent.md) · [`scenario-support-agent.md`](scenario-support-agent.md) · Srovnání: [`comparison-agent-paths.md`](comparison-agent-paths.md)

Nosná lekce: **nástroj se vybírá podle práce.** V D4–D5 stavíme **dva scénáře** a **tři cesty**; čtvrtá (Copilot Studio) přijde celá až v **D5**.

| Cesta | Kdo (D4–D5) | Scénář |
| --- | --- | --- |
| **Agent Builder** | **studenti hands-on** | HR Asistent |
| **SharePoint agent** | **instruktor jen ukáže** (tvorba = Copilot licence, studenti na PAYG netvoří) | HR Asistent |
| **Agents Toolkit** | **studenti hands-on, společně** (bez akce, agent jako kód) | Support Asistent (runbooky) |
| ~~Copilot Studio~~ | **až D5** (analytický payoff nad týmiž daty) | HR Asistent → [`../../day-5/copilot-studio/`](../../day-5/copilot-studio/) |

> [!WARNING] Ověřit k datu běhu — UI se hýbe.
> Popisky v Agent Builderu a Toolkitu se mění po měsících. Drž se úrovně kroků, ne přesných labelů; před během proklikat. Go/no-go: dostupnost Agent Builderu v PAYG tenantu (viz instructor-notes).

## Příprava (5 min)

1. Otevři na `/sites/hr-demo` **list Zaměstnanci** + knihovnu `Smlouvy` — ať třída vidí zdroj dřív než agenta (řádek listu i soubor mají stejné `Osobní číslo`).
2. Připrav v Toolkitu prázdný projekt **Declarative Agent (bez akce)** + knihovnu `Runbooky` na webu.
3. Princip z D1: **licence gate-uje funkci, permissions gate-ují obsah** — agent nepřekročí práva uživatele.

---

## Scénář A · HR Asistent (data + knowledge)

Na tabuli napiš **5 dotazů** a po každé cestě doplň sloupec ✅/❌.

| # | Dotaz | Typ | Očekávání v D4 |
| --- | --- | --- | --- |
| 1 | „Ukaž údaje a manažera osobního čísla 10018." | lookup v listu | ✅ |
| 2 | „Najdi podepsanou pracovní smlouvu pro 10024." | hledání v souboru | ✅ tam, kde je knihovna |
| 3 | „Komu propadá certifikát do 30 dnů?" | **analytika nad listem** | ❌ **žádná z těchto cest** — kdo to umí? Uvidíte **ve Studiu (D5)** |
| 4 | „Vyjmenuj lidi bez podepsaného dodatku." | filtr nad listem | ❌ → Copilot Studio (D5) |
| 5 | (soused bez práv) „Ukaž mzdový výměr 10024." | hranice práv | no response |

### Agent Builder — studenti hands-on

1. V Copilot appce → **Vytvořit agenta**, kartou Describe vygeneruj a dolaď instrukce (D4: purpose → guidelines). **Instrukce ≠ knowledge.**
2. **Knowledge:** list `Zaměstnanci` + knihovny `Certifikáty`/`Smlouvy`.
3. Pusť dotazy 1–5.

> [!NOTE] V čem se liší (co ukázat)
>
> - Dotaz 1 a 2 projdou. **Dotaz 3/4 NEspolehlivě** — Builder zdroje jen *prioritizuje*, nedělá agregaci. Řekni: „strop no-code cesty — analytiku prolomíme zítra."
> - Publikace jen sdílení / org katalog, ne marketplace, žádný source control.

### SharePoint agent — instruktor jen ukáže

> [!IMPORTANT] Proč jen demo
> Tvorba SharePoint agenta vyžaduje **Copilot licenci** — studenti na Business Basic + PAYG ho můžou *používat*, ne *tvořit* (empiricky potvrzeno 2026-07-17 — na rozdíl od Skills a Toolkitu, které na PAYG fungují). Proto tuhle cestu **instruktor jen předvede**, studenti nestaví.

1. Na `/sites/hr-demo` → **Create agent**, scope omez na **jednu knihovnu** `Smlouvy`.
2. Dotaz 2 (smlouva) projde; dotaz 1 (list) ne. **Zkus přidat list jako druhý zdroj** → shodí knihovnu.

> [!NOTE] V čem se liší (co ukázat)
>
> - **Tvrdý limit: 1 zdroj a nic jiného** (MC1255409). HR scénář (list + 2 knihovny) se do jednoho agenta nevejde.
> - Agent žije s webem (permissions, lifecycle); distribuce jen web.

**Kam patří Toolkit:** do scénáře A **ne** — SharePoint list nescopuje (ověřeno ve schématu 1.7: `list_id` = knihovna, ne list; tabulková data jen přes Dataverse/konektor/akci). Toolkit má vlastní scénář B níže, kde vyhrává.

---

## Scénář B · Support Asistent (agent jako kód) — Agents Toolkit, studenti společně

Detail a lab: [`scenario-support-agent.md`](scenario-support-agent.md) · [`../agents-toolkit/lab-toolkit-agent.md`](../../day-5/agents-toolkit/lab-toolkit-agent.md). Pro **správce**: tentýž typ agenta (čte knowledge) jako v Builderu, ale postavený jako **verzovaná, řízená konfigurace** — ne osobní klikačka. Vše v M365, **žádné API / Azure / psaní kódu**.

> [!IMPORTANT] Rozsah
> **Akci (API plugin) nestavíme** — je to vývoj mimo M365. Jen ukážeme slot `actions` a řekneme, k čemu je. „Udělej něco v tenantu" = Power Automate (D3), ne vlastní API.

4 dotazy scénáře B (čtení z runbooků):

| # | Dotaz | Co testuje | Očekávání |
| --- | --- | --- | --- |
| 1 | „Jak vyřešit ‚access denied' při uploadu?" | knowledge — runbook | ze souboru |
| 2 | „Jaká je SLA na P1?" | editorial answer | předdefinovaná odpověď |
| 3 | „Postup při výpadku tiskové fronty?" | knowledge — runbook | ze souboru |
| 4 | „Napiš básničku o počasí." | `discourage_model_knowledge` | odmítne |

### Agents Toolkit — studenti hands-on, společně (bez akce)

1. Ve VS Code s **Agents Toolkit** → **Create a New Agent/App → Declarative Agent → bez akce**. Ukaž **manifest jako konfiguraci** (`declarativeAgent.json`) verzovanou v gitu — ne program.
2. Uprav `instructions` na Support Asistenta; přidej **knowledge** `OneDriveAndSharePoint` → knihovna `Runbooky` (`items_by_url`).
3. Přidej `editorial_answers` (dotaz 2) a `discourage_model_knowledge` (dotaz 4). Ukaž **prázdný slot `actions`**: „sem by šlo API — vývoj, mimo náš rozsah."
4. **Provision** → nasadí do tenantu (žádné Azure). Empiricky funguje i studentům na PAYG (ověřeno 2026-07-17). Pusť 4 dotazy.

> [!NOTE] V čem se liší (co ukázat — pointa pro správce)
>
> - **Agent jako spravovaná konfigurace** — manifest v gitu (diff, review, historie), opakovatelný `Provision`, publikace přes schválení. Agent Builder = osobní klikačka bez source control.
> - Manifest-only funkce (`editorial_answers` až 300 párů, `discourage_model_knowledge`, `user_overrides`) nejsou v žádném UI.
> - Governance a lifecycle je **správcovský** úhel — ne „umí víc", ale „staví se řízeně".

---

## Shrnutí — každý nástroj na svou práci

| Scénář | Jádro | Vítěz | Kdo prohrává a proč |
| --- | --- | --- | --- |
| **HR Asistent** | číst list + najít soubory | **Copilot Studio (D5)** / Agent Builder | Toolkit — list nescopuje |
| **Support Asistent** | tentýž agent, ale **řízeně jako kód** | **Agents Toolkit** | Builder — osobní klikačka bez governance/ALM |

Jedna věta na nástroj:

- **Agent Builder** — „za minuty, pro každého; lookup a soubory, bez analytiky."
- **SharePoint agent** — „vlastník obsahu, jeden zdroj a nic jiného."
- **Agents Toolkit** — „agent jako **spravovaná konfigurace**: manifest v gitu, provisioning, publikace přes schválení (akce = vývoj, mimo rozsah)."
- **Copilot Studio (D5)** — „jediný, co odpoví na *kolik/kdo/kdy* nad listem."

Společné přes všechny: **agent nepřekročí práva uživatele** (negativní/permission test) a **JOIN list × knihovna neumí žádný** (to je Power Automate).

## Most do D5

Analytické dotazy 3 a 4 dnes **schválně necháváme viset** — zítra je Copilot Studio prolomí nad **týmiž daty** (`/sites/hr-demo`, list `Zaměstnanci`). „Včera jste viděli strop, dnes ho Studio prorazí."

## Stav produktu / delta

> [!WARNING] Ověřit k datu běhu — stav k 2026-07.
> Podpora listů se hýbe nejrychleji (SharePoint agents GA ~05/2026, docs lag; Copilot Studio listy = production-ready preview; manifest schema pro listy v Toolkitu k 2026-07 neexistuje — `list_id` je stále knihovna). Šablony Toolkitu (6.0) se přejmenovávají — ověřit „Declarative Agent" (stavíme bez akce). Před během ověřit go/no-go dostupnost Agent Builderu v PAYG a přesné UI labely.
