# Glosář — závazné názvosloví

Jediný zdroj pravdy pro nástroje, API, produkty a konvence používané v kurzu.
Sloučeno z glosářů GOC223 (část 1 — automatizace) a GOC224 (část 2 — AI a Copilot).

> [!WARNING] Ověřit k datu běhu — stav k 2026-07.
> Throttling limity, verze PowerShell modulů, Azure ceny, preview stavy a licenční
> balíčky se mění po měsících. Před během projet položky s tímto markerem.

---

# Část 1 — Automatizace (dny 1–3)

## PowerShell moduly (tři, ne jeden)

Kurz pokrývá všechny tři paralelně a učí, kdy který — nosný teaching point
[`day-2/powershell-deep-dive/`](day-2/powershell-deep-dive/) a
[`day-2/automation-strategy/`](day-2/automation-strategy/).

| Modul | Rozsah | Kdy použít |
|---|---|---|
| **PnP.PowerShell** | Community-driven, nejširší pokrytí SPO (weby, listy, provisioning templates, branding) | Provisioning, migrace obsahu, cokoli mimo tenant-admin scope |
| **Microsoft.Graph** (Graph PowerShell SDK) | Oficiální wrapper nad Microsoft Graph REST API | Identity, M365 skupiny, Teams, cross-workload operace, cokoli co SPO moduly nepokrývají |
| **SPO Management Shell** (`Microsoft.Online.SharePoint.PowerShell`) | Oficiální tenant-admin modul | Tenant-wide nastavení (`Set-SPOTenant`, vč. `KnowledgeAgentScope` pro D4–5), site collection admin, sharing policy |

> [!IMPORTANT] Překryv
> PnP.PowerShell a SPO Management Shell se v site-admin oblasti překrývají. Preferovat
> PnP pro čitelnost a širší funkčnost, SPO modul jen tam, kde PnP ekvivalent chybí
> (typicky nejnovější tenant-wide preview nastavení — ta často přistanou v SPO modulu dřív).

## Evoluce modulů: legacy → současnost → budoucnost

Nosná pointa pro [`day-2/automation-strategy/`](day-2/automation-strategy/): **moduly
umírají, REST API zůstává** — proto kurz učí principy nad Graph/REST, ne jen konkrétní cmdlety.

| Generace | Moduly | Stav |
|---|---|---|
| **Mrtvé (retired)** | MSOnline (`Connect-MsolService`), AzureAD/AzureADPreview | Deprecated 2024-03, nefunkční od poloviny 2025. U zákazníků se stále potkávají ve starých skriptech — umět je poznat a migrovat. Pozor: license assignment, filtering a "get all" dotazy nejde přepsat 1:1 |
| **Současnost** | Microsoft.Graph SDK, PnP.PowerShell, SPO Management Shell, ExchangeOnlineManagement (V3, REST-backed), MicrosoftTeams | Aktivně vyvíjené; PnP od 2024-09 vyžaduje vlastní app registraci (`-ClientId`) i pro interaktivní login |
| **Nastupující** | Microsoft Entra PowerShell (`Microsoft.Entra`) — přátelštější vrstva nad Graph SDK, ~98% pokrytí starých AzureAD/MSOnline cmdletů, `Enable-EntraAzureADAlias` pro rychlou migraci | Sledovat; pro identity skripty pravděpodobný budoucí default |

> [!WARNING] Ověřit k datu běhu — stav k 2026-07.
> Verze a stav modulů se mění po měsících (např. ExchangeOnlineManagement 3.10.0 nově
> vyžaduje PowerShell 7.6+). Před během projet aktuální verze všech modulů použitých v demích.

## Širší mapa modulů (mimo fokus kurzu)

Kurz jde do hloubky u trojice PnP/Graph/SPO (fokus = SharePoint Online). Zbytek M365
ekosystému jen jako mapa — studenti mají vědět, že existují, ne je ovládat:

| Modul/nástroj | Workload | Poznámka |
|---|---|---|
| **ExchangeOnlineManagement** (EXO V3) | Exchange Online + Security & Compliance (`Connect-IPPSSession`) | REST-backed, cert-based app-only podporováno |
| **MicrosoftTeams** | Teams admin (týmy, policies, telefonie) | |
| **Microsoft.PowerApps.Administration.PowerShell** | Power Platform admin (environments, DLP) | service principal nutný při MFA |
| **Az PowerShell** | Azure resources | používá se v D3 (Functions, Blob) — ne M365 samotné |
| **CLI for Microsoft 365** (`@pnp/cli-microsoft365`) | cross-workload, npm/Node | úzká role: CI/CD pipeline a tooling — ne obecná alternativa PnP.PowerShell pro administraci; překryv s PnP je u SPO ~80 % a učit oba na stejný problém nedává smysl |

## TypeScript/Node cesta

Alternativa k PowerShellu pro vývojářské týmy: **Graph JS SDK**
(`@microsoft/microsoft-graph-client` + `@microsoft/microsoft-graph-types`) s
`@azure/identity` credentials (stejná auth matice jako PowerShell) a **PnPjs**
(`@pnp/sp`) pro SPO-native volání. Detail:
[`day-3/opt-node-typescript/explainer-typescript-graph.md`](day-3/opt-node-typescript/explainer-typescript-graph.md).

## App registrace vs Enterprise Application

- **App registrace** (application object) = globální šablona aplikace — credentials,
  požadované permissions, `signInAudience`; žije jen v domovském tenantu.
- **Enterprise Application** (service principal) = lokální instance v každém tenantu, kde
  aplikace působí — udělený consent, assignment, sign-in logy.
- **Single-tenant** (`AzureADMyOrg`) je doporučený default; **multi-tenant**
  (`AzureADMultipleOrgs`) jen s reálným scénářem a consent governance.
- Detail a practices: [`day-2/automation-strategy/explainer-app-registrations-enterprise-apps.md`](day-2/automation-strategy/explainer-app-registrations-enterprise-apps.md).

## Autentizační strategie (app registration)

| Režim | Kdy | Poznámka |
|---|---|---|
| **Interactive (delegated)** | ad-hoc admin session, vývoj | Browser/WAM popup; pozor na default browser identity |
| **Device code** | headless/vzdálené prostředí, MFA | Kód zadaný v libovolném browseru/profilu |
| **Certificate (app-only)** | dávkové operace, produkční automatizace | Bez promptu; cert thumbprint + ClientId + TenantId |
| **Managed identity** | Azure-hosted automatizace (Functions, Runbooks) | Žádný spravovaný secret/cert — identita vázaná na Azure resource |

**Least privilege princip:** aplikační oprávnění (application permissions) se udělují
na úrovni celého tenantu — každé navíc je rozšíření útočné plochy. Preferovat delegated
tam, kde to dává smysl, a u app-only vždy sepsat přesný seznam permissions
s odůvodněním (viz [`day-2/security-hardening/`](day-2/security-hardening/)).

**Public client vs confidential client** — dělení aplikací podle toho, zda umí udržet
tajemství. **Confidential client** běží mimo dosah uživatele (server, Azure Function)
a při přihlášení prokazuje sám sebe vlastním credentialem (certifikát, secret).
**Public client** běží na zařízení uživatele (PowerShell konzole, desktop, mobil) —
cokoli zadrátovaného by šlo vytáhnout, proto se prokazuje **jen uživatel**, aplikace
nic nedokazuje. **Jak Entra typ pozná:** primárně z platformy redirect URI (*Web* =
confidential, *Mobile and desktop applications* / *SPA* = public — proto `-Interactive`
s redirectem `http://localhost` funguje bez dalšího nastavování); u flow **bez redirect
URI** (device code, ROPC) rozhoduje fallback přepínač *Allow public client flows*
(`isFallbackPublicClient`) — vypnutý = `AADSTS7000218`. Certifikátový app-only režim
je confidential z podstaty. Jedna app registrace může podporovat obojí — jako kurzovní
`<jmeno.prijmeni>-course-app`. Mnemotechnika: *public = prokazuje se člověk,
confidential = prokazuje se aplikace.*

**FIDO2 / WebAuthn (passkey)** — standard pro passwordless, phishing-resistant
přihlášení **člověka**: privátní klíč v hardwaru (YubiKey, TPM, telefon) podepisuje
výzvu služby, heslo neexistuje a podpis je vázaný na doménu (falešná přihlašovací
stránka ho nedostane). Nezaměňovat s **PIV** — tentýž YubiKey umí obojí, ale FIDO2
drží identitu člověka, PIV certifikát u nás credential aplikace
(viz [`day-2/certificates-and-keys/`](day-2/certificates-and-keys/)). Stejná pointa
obou: privátní klíč, který fyzicky nejde zkopírovat.

## Microsoft Graph — inženýrské pojmy

| Pojem | Co to je |
|---|---|
| **`$batch`** | Až 20 requestů v jednom HTTP volání — snižuje round-tripy, ale batch-level throttling se počítá jinak než jednotlivé requesty |
| **Delta query (`delta()`)** | Inkrementální sync — server vrací jen změny od posledního `deltaLink`, ne plný re-scan |
| **Throttling (429)** | Graph vrací `Retry-After` header — respektovat, ne pevný `Start-Sleep` |
| **Paging (`@odata.nextLink`)** | Výsledky nad limit stránky se stránkují — chybějící loop = tichá ztráta dat, ne chyba |
| **Klasifikace chyb** | 429 (throttling, retry) vs 5xx (transient, retry s backoff) vs 4xx mimo 429 (permanentní, nelogovat jako transient) |

## Azure integrační vzory

| Nástroj | Kdy použít |
|---|---|
| **Azure Logic Apps** | Nízkokódová orchestrace, konektory, byznys uživatel/citizen dev spoluautor |
| **Azure Functions** | Pro-code, event-driven, potřeba plné kontroly nad retry/error handling |
| **Azure Automation Runbooks** | PowerShell-native scheduled/dlouhoběžící úlohy, historicky pro on-prem hybrid |
| **Event Grid** | Pub/sub distribuce eventů (vč. Graph change notifications, Blob eventů) |
| **Graph change notifications (webhooks)** | Subscription na změny v M365 datech (max platnost subscription dle typu resource — nutno obnovovat) |

## SIEM pipeline (Azure Blob)

Standardní tvar pipeline v [`backlog/opt-siem-blob-integration/`](backlog/opt-siem-blob-integration/):
`aplikace → Azure Blob Storage → Event Grid → Azure Function → SIEM`
(Sentinel/Splunk/QRadar dle zákazníka).

- **Schéma logů**: strukturované (JSON), minimalizace PII (žádné celé UPN/e-maily v plain textu, kde to jde — hash/pseudonymizace).
- **Retence**: log retence odděleně od retence zdrojových dat — GDPR/compliance požadavky se liší.
- **Spolehlivost**: batching (snížení počtu Function invocations), retry s exponenciálním backoff, dead-letter queue pro trvale selhávající zprávy.
- **KQL** (Kusto Query Language) — dotazovací jazyk nad Log Analytics/Sentinel, používaný k validaci ingestované telemetrie a stavbě dashboardů.

## Provisioning

**PnP provisioning (tenant templates, `.pnp` balíčky)** — deklarativní XML/JSON šablona
+ PnP.PowerShell/PnP Framework aplikuje na cílový web; plná kontrola, vyžaduje vlastní
orchestraci žádanek a lifecycle. (3rd-party governance vrstvy typu Orchestry jsou mimo
rozsah tohoto běhu — pokrývají je plné kurzy GOC223/GOC224.)

## Migrační nástroje

| Nástroj | Kategorie | Poznámka |
|---|---|---|
| **SPMT** (SharePoint Migration Tool) | Microsoft, zdarma, desktop | SP Server 2010-2019 + file shares → SPO/OneDrive/Teams; PS modul `Microsoft.SharePoint.MigrationTool.PowerShell` (`Register-SPMTMigration` → `Add-SPMTTask` → `Start-SPMTMigration`) se instaluje s klientem, **jen Windows PowerShell 5.x** |
| **Migration Manager** | Microsoft, zdarma, cloud (SharePoint admin centrum) | agent-based škálování pro file shares + cloud zdroje (Box, Dropbox, Google Workspace); **ne** pro on-prem SP weby |
| **SMAT** (SharePoint Migration Assessment Tool) | Microsoft, zdarma, CLI | pre-migrační scan on-prem farmy (assess & remediate) |
| **ShareGate / AvePoint Fly / Quest Content Matrix** | 3rd-party, komerční | kupují fidelitu (verze/permissions), tenant-to-tenant a reporting; ShareGate má vlastní PS modul (`Copy-Content`, také jen Windows PowerShell) |

Detail a rozhodovací osa: [`backlog/migration-patterns/explainer-migration-tools.md`](backlog/migration-patterns/explainer-migration-tools.md).

## Migrace — klíčové limity

> [!WARNING] Ověřit k datu běhu — stav k 2026-07.

- **List view threshold**: 5000 = limit na to, kolik položek smí projít **jeden dotaz** — ne strop velikosti listu (ten je v milionech) a ne throttling. V SPO se nedá zvýšit.
- **Indexovaný sloupec**: pomocná struktura, která dotaz zúží pod threshold; filtr/řazení na indexovaném sloupci projde i nad velkým listem. Limit ~20 indexů na list, nelze indexovat vícehodnotové a počítané sloupce ani víceřádkový text; zavádět dokud je list malý. `Set-PnPField -List X -Identity Y -Values @{Indexed=$true}`.
- **Throttling vs threshold**: threshold = *jeden dotaz je moc velký* (chyba okamžitě), throttling = *voláš moc často* (429/503 + `Retry-After`). Dvě různé věci, dvě různá řešení: index a stránkování vs. backoff a dávky.
- **Dekorace user agenta**: vlastní REST/CSOM volání do SPO označit `NONISV|Firma|Aplikace/1.0` — nedekorovaný provoz je throttlován agresivněji (PnP.PowerShell si UA nastavuje sám).
- Detail a checklist: [`day-3/graph-fundamentals/explainer-large-lists.md`](day-3/graph-fundamentals/explainer-large-lists.md).
- **Verze souborů**: výchozí retence verzí (major/minor) násobí objem migrovaných dat — řešit před migrací, ne po ní.
- **Search crawl delay**: po migraci obsah není okamžitě vyhledatelný — plánovat cutover s rezervou na re-index.
- **Wave planning**: rozdělení migrace do vln dle rizika/velikosti/závislostí, ne dle abecedy — kritické weby v pozdější vlně s delším bufferem na rollback.

## Formáty (proč je studenti potřebují)

Výuka formátů: [`day-1/formats-fundamentals/`](day-1/formats-fundamentals/).

| Formát | Role v kurzu |
|---|---|
| **JSON** | Graph/REST payloady, `tasks.json`, vstupy labů (Lab 1, Lab 5), column/view formatting |
| **YAML** | CI/CD pipeline definice, front-matter, konfigurační soubory — jen čteme |
| **XML** | PnP provisioning šablony, CAML dotazy, starší SPO REST (ATOM) — poznat a zorientovat se |
| **CSV** | tabulární vstupy/výstupy (`Import-Csv`/`Export-Csv`), seznamy účtů, reporty pro Excel |
| **Markdown** | materiály kurzu, dokumentace, PR popisy, agent instrukce |

**UTF-8** — jediné kódování kurzu: API (Graph/REST) mluví UTF-8 vždy, problémy s českou
diakritikou vznikají až na hranici se soubory. PowerShell 7 čte i píše UTF-8 jako
default; ve skriptech přesto psát `-Encoding utf8` explicitně (kvůli Windows
PowerShell 5.1, kde default UTF-8 není) a **CSV pro Excel exportovat s
`-Encoding utf8BOM`** — bez BOM český Excel diakritiku rozbije.

## Vývojářské nástroje

| Nástroj | Role v kurzu |
|---|---|
| **VS Code** | primární editor — workspace, tasks.json, launch.json (ladění PowerShell/Node), formátování |
| **M365 Copilot Chat (free)** | AI asistent kurzu pro přípravu a testování skriptů — součást firemního účtu, enterprise data protection; vždy s priming promptem ([`day-1/formats-fundamentals/copilot-priming-prompt.md`](day-1/formats-fundamentals/copilot-priming-prompt.md)) a bez tajných klíčů/tenant ID v promptu. (GitHub Copilot = placená editor-integrace, v kurzu jen zmínka.) |
| **Git** | verzování skriptů — malé commity, pull před push, PR/code review; hosting (GitHub vs Azure DevOps vs self-hosted) a doporučení pro veřejnou správu: [`day-1/vscode-copilot-env/explainer-git-hosting.md`](day-1/vscode-copilot-env/explainer-git-hosting.md) |

---

# Část 2 — AI a Copilot (dny 4–5)

## Produktové názvy

Značka „SharePoint Premium" byla rozdělena. **Není** 1:1 přejmenovaná na jeden produkt.

| Používat | Dřívější názvy | Rozsah | Poznámka |
|---|---|---|---|
| **Document processing for Microsoft 365** | Syntex → SharePoint Premium (content-AI část) | vytěžování dokumentů, PAYG | prioritní služby: Autofill columns, Document translation, OCR, eSignature |
| **eSignature** | SharePoint eSignature | podepisování dokumentů | služba pod Document processing; v UI stále „SharePoint eSignature" |
| **SharePoint Advanced Management (SAM)** | (dříve pod deštníkem „Premium") | governance webů/OneDrive | dvě licenční cesty: Plan 1 add-on ($3/user/měs), NEBO ≥1 M365 Copilot licence v tenantu (výjimka: restricted site creation by apps = jen Plan 1) |
| **Microsoft 365 Backup** | „SharePoint backup" | záloha SharePoint + OneDrive + Exchange | samostatný produkt |
| **Microsoft 365 Archive** | „SharePoint archive" | studená data / cold storage | samostatný produkt |
| **Copilot in SharePoint** | Knowledge Agent → AI in SharePoint | Copilot zážitek nad weby (vč. Skills) | opt-out preview, default-on pro Copilot licence |
| **Agent 365** | (nový, GA 1. 5. 2026) | governance/security control plane pro AI agenty | per-user, $15/měs nebo v E7; agenti se nelicencují, licencuje se uživatel |

> [!IMPORTANT] Backend vs. brand
> Microsoft dokumentace a URL stále nesou „syntex" (`learn.microsoft.com/microsoft-365/syntex/`).
> Studenty na to upozornit, ať je URL nezmate.

## Dva PAYG modely (nezaměňovat)

| Model | Co platí | Metrika | Kde |
|---|---|---|---|
| **Document processing PAYG** | vytěžování dokumentů, OCR, překlad, eSignature | Azure metry (za stránku/dokument) | M365 admin center → billing |
| **Copilot Credits PAYG** | Copilot Chat (nad tenant daty) + použití SharePoint agents + **Skills / Copilot in SharePoint** a **tvorba/nasazení agentů přes Agents Toolkit** (empiricky fungují i na PAYG bez Copilot licence — MS nedokumentuje, ověřeno na kurzu 2026-07-17) | Copilot Credits ($0,01/kredit) | M365 admin center → PAYG billing policy + Azure |

Backup i Archive vyžadují nastavený (ex-Syntex) PAYG billing — stejná plumbing jako
Document processing, ale samostatné produkty a pricing.

**Další PAYG metry (stejná Azure plumbing):** SharePoint/OneDrive Storage (nad kvótu,
public preview 2026), Microsoft 365 Backup (dle objemu), Microsoft 365 Archive (cold
storage nad kvótu). Společný jmenovatel: všechny se účtují přes připojenou **Azure
subscription + resource group**; setup vyžaduje SharePoint nebo Global Admin.

> [!NOTE] Nosný teaching point: **nezaměňovat Document processing PAYG vs Copilot
> Credits**. Storage/Backup/Archive jsou další PAYG metry, ale jiná kategorie
> (úložiště/data), ne AI zpracování.

## Licence vs. permissions (nosný princip)

- **Licence** gate-uje *přístup k funkci*. MS dokumentuje **Copilot in SharePoint a
  Skills jako license-only**, ale **empiricky fungují i na PAYG bez Copilot licence** —
  tvorba i použití (ověřeno na kurzu 2026-07-17; Microsoft to takto nedokumentuje).
  Totéž platí pro **tvorbu a nasazení agentů přes Agents Toolkit** na PAYG. Copilot
  Chat a použití SharePoint agents = licence NEBO PAYG. **Výjimka: tvorba SharePoint
  agenta Copilot licenci vyžaduje** (empiricky potvrzeno — použití jde přes PAYG).
- **SharePoint permissions** gate-ují *kdo funkci použije* (Edit = tvorba
  Skill/agenta, View = spuštění).
- Skills nemají vlastní SKU ani PAYG metr.

## Aktuální licenční reálie

> [!WARNING] Ověřit k datu běhu — stav k 2026-07.

- Stack: E1 → E3 → E5 → **E7** (Frontier Suite; balí E5 + Copilot + Agent 365).
- **Copilot Business** (Business Standard/Premium base, ≤300 uživatelů) vs **Copilot
  Enterprise** — in-app funkce stejné; enterprise-only jsou Researcher/Analyst/Facilitator,
  model choice, Copilot Tuning.
- **Basic vs Premium Copilot split** — plný in-app Copilot jen v Premium; Basic = chat.
- Bezplatný Copilot Chat/Basic **nestačí** na grounding nad SharePointem — nutná placená
  Copilot licence nebo Copilot Credits PAYG.

## AI subprocesoři (modely třetích stran)

> [!WARNING] Ověřit k datu běhu — stav k 2026-07.
> Copilot může vedle OpenAI používat **Anthropic** modely. Anthropic = **Microsoft
> subprocesor** (pod Microsoft DPA), ale inference **mimo Azure / EU Data Boundary**
> (AWS/GCP, US). **EU/EFTA/UK: default vypnuto**; jinde on-by-default. **Preview models
> with Data Retention** (Claude Fable 5, Mythos 5) = Anthropic jako **nezávislý
> procesor** (drží data), vždy opt-in. **Copilot in SharePoint / Skills běží na OpenAI
> modelu — na Anthropicu nezávisí.** Pro krajský úřad klíčové compliance téma. Detail:
> [`day-5/copilot-admin/explainer-ai-subprocessors.md`](day-5/copilot-admin/explainer-ai-subprocessors.md).

## Vývojářské nástroje (pro-code agenti)

| Nástroj | Co to je | Licenční dotyk |
|---|---|---|
| **Microsoft 365 Agents Toolkit** | nástupce Teams Toolkitu; VS Code / Visual Studio / GitHub Copilot / CLI; scaffolduje deklarativní a custom engine agenty, manifest, provisioning, MCP akce | zdarma |
| **GitHub Copilot** | AI asistent při psaní kódu; Agents Toolkit je pro něj dostupný | vlastní licence (mimo M365) |
| **Copilot Studio** | low-code stavba agentů | Copilot Credits / M365 Copilot licence |
| **Agent Builder** | lightweight tvorba deklarativních agentů přímo v M365 Copilot (bez opuštění appky) | M365 Copilot licence / Copilot Credits — ověřit k datu běhu |

Rozhodovací osa (deklarativní agent source-controlled vs. Copilot Studio low-code vs.
pro-code): [`day-4/copilot-agents/comparison-agent-paths.md`](day-4/copilot-agents/comparison-agent-paths.md).
Agenti jako kód sedí na repo-as-code přístup automatizačních dnů 1–3.

## Azure AI služby (mimo M365)

| Používat | Dřívější názvy | Poznámka |
|---|---|---|
| **Azure AI services** | Azure Cognitive Services | zastřešující rodina Azure AI služeb |
| **Azure AI Document Intelligence** | Form Recognizer (→ součást ex-Cognitive Services) | robustnější alternativa vytěžování dokumentů vůči AI Builder / Document processing |

> [!IMPORTANT] Názvosloví
> „Azure Cognitive Services" je zastaralý brand — v materiálech používat **Azure AI
> services**. Účtování jde přes Azure subscription (třetí kategorie vedle obou M365
> PAYG modelů).
