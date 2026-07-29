# Mapa API M365 & SPO — minulost, současnost a budoucnost skriptování

> Typ: povinný · Den: 1 · Odhad: 55 min výklad + 20 min cvičení Graph Explorer

## Cíle
- Vysvětlit vlastními slovy, co je API a jak vypadá jedno REST volání (sloveso + URL +
  hlavičky + JSON odpověď) — bez psaní kódu.
- Orientovat se v mapě API nad Microsoft 365: co pokrývá Microsoft Graph, co SharePoint
  REST/CSOM a proč obojí existuje vedle sebe.
- Umět zařadit skriptování v čase: **minulost** (SOAP → CSOM/JSOM → REST; MSOnline/AzureAD
  → SPO Management Shell → PnP), **současnost** (PS7 + PnP/Graph moduly + AI asistent)
  a **budoucnost** (AI agenti volající tatáž API) — s pointou: **moduly umírají,
  REST API zůstává**.
- Zavolat si první vlastní Graph dotaz v Graph Exploreru
  ([`exercise-graph-explorer.md`](exercise-graph-explorer.md)).
- Odnést si argumenty, proč se automatizace M365 nebát: data zůstávají ve vašem tenantu,
  přístup řídí permissions model a všechno je auditovatelné.

## Výklad

### Co je API — jedno volání beze strachu
API není nic víc než smluvený způsob, jak si dva programy říkají o data. REST API nad
M365 mluví přes HTTPS: pošlete **sloveso** (`GET` = přečti, `POST` = vytvoř, `PATCH` =
uprav, `DELETE` = smaž), **URL** (co chcete), **hlavičky** (kdo jste — token) a dostanete
zpět **JSON** (strukturovaná odpověď). Ukázka, kterou za 20 minut uvidíte živě:

```http
GET https://graph.microsoft.com/v1.0/me
Authorization: Bearer <token>
```

Odpověď je JSON s vaším jménem, e-mailem a ID. Nic víc v základu není — všechno ostatní
(moduly, SDK, nástroje) jsou jen pohodlnější obálky nad tímhle principem.

### Mapa API nad Microsoft 365
- **Microsoft Graph** (`graph.microsoft.com`) — jednotná brána nad celým M365: uživatelé,
  skupiny, weby, soubory, pošta, Teams, licence. Strategické API, do kterého Microsoft
  investuje; nové funkce přicházejí sem.
- **SharePoint REST API** (`<tenant>.sharepoint.com/_api/...`) — starší, ale plnohodnotné
  API specifické pro SharePoint; umí věci, které Graph dosud nepokrývá (jemná práce
  s listy, poli, permissions na úrovni webu).
- **CSOM** (Client-Side Object Model) — .NET knihovna nad SharePoint API z éry
  SharePoint 2010/2013; dnes ji potkáte hlavně uvnitř starších nástrojů a skriptů.
- **Admin API** — správcovská rozhraní (SPO admin, Entra), typicky konzumovaná přes
  PowerShell moduly.

Klíčová mentální mapa: PowerShell moduly (PnP.PowerShell, Microsoft.Graph, SPO Management
Shell) **nejsou třetí svět vedle API** — jsou to wrappery nad těmito dvěma REST rozhraními.
Otázka nikdy nezní „PowerShell, nebo API?", ale „wrapper, nebo přímé volání?"
(rozhodovací osu rozebere [`../../day-2/automation-strategy/`](../../day-2/automation-strategy/)).

### Minulost — proč je dnešní krajina, jaká je
| Éra | Nástroj/API | Stav dnes |
|---|---|---|
| 2007– | SOAP web services (`_vti_bin/*.asmx`) | mrtvé, jen v legacy kódu |
| 2010–2013 | CSOM (.NET) a JSOM (JavaScript) | CSOM přežívá v nástrojích, JSOM mrtvý se SP add-in modelem |
| 2010–2016 | Sandbox solutions (custom kód uvnitř SPO) | code-based **vypnuté 2016** |
| 2013–2026 | **SharePoint Add-ins** (add-in model) + **ACS** auth (`AppRegNew.aspx`, app-only přes `accesscontrol.windows.net`) | **vypnuté 2. 4. 2026** (nové tenanty už od 11/2024) |
| 2013– | „JS injection" — JSLink, Script Editor, ScriptLink custom actions | na modern pages nefunguje; custom script od 11/2024 Microsoft plošně vypíná |
| 2013– | SharePoint REST `_api` | živé, SPO-specifické mezery Graphu |
| 2012– | MSOnline (`Msol*`), později AzureAD modul | **oba vypnuté** — viz glosář, umět poznat ve starých skriptech |
| 2014– | SPO Management Shell (`*-SPO*`) | živé, tenant-admin nastavení |
| 2014– | PnP — community projekt (OfficeDevPnP → PnP.PowerShell) | živé, nejširší SPO pokrytí |
| 2015– | Microsoft Graph + Graph PowerShell SDK | živé, strategické |

Kde jste mrtvé vrstvy potkávali (a možná ještě potkáte u sebe): **Add-iny** byly
oficiální cesta, jak do SharePointu dostat aplikace a formuláře (App Catalog éry 2013);
**ACS** k nim dával app-only přístup — registrace přes `AppRegNew.aspx` a „client
secret na rok" bez admin consentu. **JS injection** (Script Editor webpart, JSLink)
byl způsob, jak si upravit chování stránek a formulářů JavaScriptem. Všechny tři cesty
letos definitivně skončily nebo končí — a firmy, které na nich mají řešení, to zjišťují
právě teď. Náhrada je vždy stejná dvojice: **Entra app registrace** (identita — D2
tohoto kurzu) a **SPFx** (customizace — viz budoucnost níže).

Dvě ponaučení z té tabulky: (1) každých pár let nějaká vrstva zemře — kdo rozumí REST
principu pod ní, přepíše řešení; kdo zná jen konkrétní nástroj, začíná od nuly.
(2) Staré skripty a weby u zákazníků jsou plné mrtvých vrstev — umět je poznat je
samostatná dovednost (závazný přehled: [`../../GLOSSARY.md`](../../GLOSSARY.md)).

### Současnost — čím se skriptuje dnes (a čím tento kurz)
- **PowerShell 7** (multiplatformní) + tři moduly: PnP.PowerShell, Microsoft.Graph SDK,
  SPO Management Shell — detail v D2 ([`../../day-2/powershell-deep-dive/`](../../day-2/powershell-deep-dive/)).
- **Microsoft Graph jako strategická brána** — nové funkce M365 přicházejí nejdřív sem;
  SDK se generují ze schématu API (PowerShell, TypeScript, C#… — jazyk je volba týmu,
  viz [`../../day-3/node-typescript/`](../../day-3/node-typescript/)).
- **AI asistent u psaní** — skript dnes typicky vzniká konverzací (Copilot Chat navrhne,
  člověk čte a testuje). To je workflow, který se učíme od dnešního odpoledne — včetně
  mantinelů proti fantazii ([`../formats-fundamentals/copilot-priming-prompt.md`](../formats-fundamentals/copilot-priming-prompt.md)).
- **Skript jako spravovaný artefakt** — žije v gitu, prochází review, běží pod vlastní
  identitou s vymezenými právy (D2) — ne jako `.ps1` v e-mailové příloze.

### Budoucnost — tři podporované cesty vývoje
Za každou vypnutou technologii z tabulky výše existuje jasný nástupce. Microsoft dnes
investuje do tří směrů — a všechny tři stojí na témže základu (Graph API + Entra
identita), který se učíme ve dnech 1–3:

- **AI a Copilot** — z „píšu skript" na „zadávám záměr": Copilot Chat jako asistent
  psaní skriptů (od dnešního odpoledne), Copilot agenti (D4–5) jako konzumenti týchž
  Graph API — místo člověka s konzolí volá API agent s nástrojem. Kdo rozumí API
  a permissions modelu, rozumí i agentům; je to stejná stavebnice.
- **SPFx (SharePoint Framework)** — jediná podporovaná cesta customizace SharePointu:
  webparty a rozšíření stránek v TypeScriptu, nasazované přes App Catalog, s consentem
  řízeným přes Entra. Přímý nástupce Add-inů i JS injection — co dřív dělal Script
  Editor „na divoko", dělá dnes SPFx řízeně a auditovatelně. V tomto běhu jen mapa
  (jazykový základ ukazuje [`../../day-3/node-typescript/`](../../day-3/node-typescript/));
  hloubka je téma plného kurzu GOC223.
- **API-first / Graph** — Microsoft postupně směruje vše na Graph (Kiota-generované
  SDK, nová admin API); cmdlety a moduly se budou dál měnit, kontrakt API zůstává.
  S tím roste význam **identity a least privilege** (D2): každý běžící kód — skript,
  webpart i agent — má vlastní, auditovatelnou a odvolatelnou identitu.

Prakticky: investice do pochopení REST/Graph a permissions (dny 1–3) je společný
jmenovatel všech tří cest — přesně ta část, která přežije další generační výměnu.

### Proč se toho nebát — tři argumenty pro vaši firmu
1. **Data neopouštějí váš tenant.** API volání jde přímo do vašeho M365 pod identitou,
   kterou jste vydali a můžete kdykoli zneplatnit. Žádná třetí strana, žádná kopie dat.
2. **Přesně vymezená oprávnění.** Skript dostane jen permissions, které mu admin schválí
   (např. „čti weby, nic víc") — na rozdíl od člověka s admin heslem. Celý D2 je o tom,
   jak to udělat pořádně.
3. **Všechno je auditovatelné.** Každé volání nese identitu aplikace a zapisuje se do
   audit logu — automatizace je *čitelnější* stopa než ruční klikání.

## Cvičení — Graph Explorer hands-on
Viz [`exercise-graph-explorer.md`](exercise-graph-explorer.md) — každý účastník si
zavolá své první Graph dotazy sám (`/me`, `/me/joinedTeams`, `$select`), bez psaní
kódu. Instruktor na závěr ukáže totéž volání jednou řádkou PowerShellu — ochutnávka D2.

## Klíčové rozlišení
- **API vs modul/wrapper** — modul je pohodlí, API je podstata; modul může zemřít, API má
  závazky kompatibility.
- **Microsoft Graph vs SharePoint REST** — jedna brána na všechno vs starší specialista na
  SPO detaily; v praxi se kombinují.
- **`v1.0` vs `beta`** (Graph) — `beta` je na hraní, do skriptů pro firmu patří `v1.0`.
- **Strach vs řízené riziko** — automatizace není „něco, co nám sáhne na data", ale
  identita s přesně vymezenými právy a auditní stopou.

## Zdroje (Microsoft)
- [Microsoft Graph overview](https://learn.microsoft.com/en-us/graph/overview)
- [Get to know the SharePoint REST service](https://learn.microsoft.com/en-us/sharepoint/dev/sp-add-ins/get-to-know-the-sharepoint-rest-service)
- [Graph Explorer](https://developer.microsoft.com/en-us/graph/graph-explorer)
- [PnP PowerShell](https://pnp.github.io/powershell/)

## Stav produktu / delta
> [!WARNING]
> Ověřit k datu běhu — lineage tabulka (MSOnline/AzureAD retirement, PnP requirements)
> se hýbe; závazný stav drží [`../../GLOSSARY.md`](../../GLOSSARY.md), tento modul z něj
> jen čte. Deprecation data k 2026-07: SharePoint Add-ins a ACS vypnuté 2. 4. 2026
> (nové tenanty od 11/2024), custom script plošně vynucovaně vypínán od 11/2024 —
> Microsoft u ACS zveřejnil možnost placeného odkladu pro largest customers; ověřit
> aktuální stav retirement FAQ před během.
