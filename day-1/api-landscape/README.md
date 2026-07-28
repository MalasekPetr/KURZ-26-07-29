# Mapa API M365 & SPO + historie nástrojů

> Typ: povinný · Den: 1 · Odhad: 60 min výklad + 15 min demo & diskuze

## Cíle
- Vysvětlit vlastními slovy, co je API a jak vypadá jedno REST volání (sloveso + URL +
  hlavičky + JSON odpověď) — bez psaní kódu.
- Orientovat se v mapě API nad Microsoft 365: co pokrývá Microsoft Graph, co SharePoint
  REST/CSOM a proč obojí existuje vedle sebe.
- Znát historickou linii skriptovacích nástrojů (SOAP → CSOM/JSOM → REST → Graph;
  MSOnline/AzureAD → SPO Management Shell → PnP → Graph PowerShell) a umět z ní vyvodit
  pointu: **moduly umírají, REST API zůstává**.
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

### Historie nástrojů — proč je dnešní krajina, jaká je
| Éra | Nástroj/API | Stav dnes |
|---|---|---|
| 2007– | SOAP web services (`_vti_bin/*.asmx`) | mrtvé, jen v legacy kódu |
| 2010–2013 | CSOM (.NET) a JSOM (JavaScript) | CSOM přežívá v nástrojích, JSOM mrtvý se SP add-in modelem |
| 2013– | SharePoint REST `_api` | živé, SPO-specifické mezery Graphu |
| 2012– | MSOnline (`Msol*`), později AzureAD modul | **oba vypnuté** — viz glosář, umět poznat ve starých skriptech |
| 2014– | SPO Management Shell (`*-SPO*`) | živé, tenant-admin nastavení |
| 2014– | PnP — community projekt (OfficeDevPnP → PnP.PowerShell) | živé, nejširší SPO pokrytí |
| 2015– | Microsoft Graph + Graph PowerShell SDK | živé, strategické |

Dvě ponaučení z té tabulky: (1) každých pár let nějaký modul zemře — kdo rozumí REST
principu pod ním, přepíše skript za odpoledne; kdo zná jen cmdlety nazpaměť, začíná od
nuly. (2) Staré skripty u zákazníků jsou plné mrtvých vrstev — umět je poznat je
samostatná dovednost (závazný přehled: [`../../GLOSSARY.md`](../../GLOSSARY.md)).

### Proč se toho nebát — tři argumenty pro vaši firmu
1. **Data neopouštějí váš tenant.** API volání jde přímo do vašeho M365 pod identitou,
   kterou jste vydali a můžete kdykoli zneplatnit. Žádná třetí strana, žádná kopie dat.
2. **Přesně vymezená oprávnění.** Skript dostane jen permissions, které mu admin schválí
   (např. „čti weby, nic víc") — na rozdíl od člověka s admin heslem. Celý D2 je o tom,
   jak to udělat pořádně.
3. **Všechno je auditovatelné.** Každé volání nese identitu aplikace a zapisuje se do
   audit logu — automatizace je *čitelnější* stopa než ruční klikání.

## Demo (instruktor)
Graph Explorer (`https://developer.microsoft.com/graph/graph-explorer`): přihlášení
kurzovním účtem, `GET /me`, `GET /me/joinedTeams`, ukázat surový JSON a záložku
permissions u každého dotazu. Totéž volání pak jednou řádkou PowerShellu — ochutnávka D2.

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
> jen čte.
