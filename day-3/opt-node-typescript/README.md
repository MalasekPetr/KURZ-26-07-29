# Node.js & TypeScript — druhá cesta k API

> Typ: **volitelný** · Den: 3 · Odhad: 20 min (výklad + krátké demo)

## Cíle
- Vědět, že PowerShell není jediná cesta: tatáž API (Graph, SharePoint) lze volat
  z **Node.js/TypeScript** — stejná auth matice z D2, jiný jazyk.
- Umět zařadit, kdy TS/Node dává smysl (vývojářský tým, Azure Functions v TS, CI/CD,
  návaznost na SPFx) a kdy zůstat u PowerShellu.
- Poznat stavební bloky: Node.js (runtime), npm (balíčky), TypeScript (typy nad
  JavaScriptem), Graph JS SDK.

## Výklad

### Node.js, npm, TypeScript — třicetivteřinová mapa
**Node.js** je běhové prostředí JavaScriptu mimo prohlížeč (protějšek PowerShellu 7 pro
jiný jazyk). **npm** je jeho PowerShell Gallery — balíčky se deklarují v `package.json`
(zase JSON!). **TypeScript** přidává JavaScriptu typy: editor napoví strukturu Graph
odpovědi dřív, než skript spustíte — u API s tisíci entitami znatelná pomoc.

### Stejný princip, jiný jazyk
Celý kurz stojí na větě „moduly jsou wrappery nad REST API" — a tady je důkaz: tentýž
`GET /sites/root`, tentýž certifikát z D2, jen volaný z jiného jazyka. Auth módy
z [`../../day-2/powershell-deep-dive/`](../../day-2/powershell-deep-dive/) mají 1:1
protějšky v `@azure/identity`. Detail a ukázkový kód:
[`explainer-typescript-graph.md`](explainer-typescript-graph.md).

### Kdy kterou cestu
PowerShell: admin úlohy s hotovými cmdlety (provisioning, tenant nastavení) — v TS
byste znovu vynalézali, co cmdlety umí. TS/Node: vývojářský tým, skripty žijící vedle
aplikačního kódu, Azure Functions v TS, CI/CD s node image, výhled na SPFx (vývoj
webpartů — mimo tento běh). Pro účastníky tohoto kurzu je PowerShell správný start;
tenhle blok je mapa, ne pozvánka k přestupu.

## Demo (živě)
Připravený TS skript (z explaineru): `npm install`, spuštění s certifikátovou identitou
z D2, výstup = tentýž seznam webů jako v Labu 4. Ukázat IntelliSense nad Graph typy
(`@microsoft/microsoft-graph-types`) — „editor zná odpověď dřív než server".

## Klíčové rozlišení
- **Node.js vs PowerShell 7** — dva runtime pro skriptování; oba multiplatformní, oba
  volají tatáž REST API.
- **npm + `package.json` vs PowerShell Gallery + `-RequiredVersion`** — stejná disciplína
  správy závislostí (pinování verzí, D2), jiný ekosystém.
- **TypeScript vs JavaScript** — typy = nápověda a kontrola při psaní; kompiluje se do
  JavaScriptu, který Node spouští.
- **TS/Node vs PowerShell volba** — podle týmu a cíle běhu, ne podle módy; kritéria
  v explaineru.

## Zdroje (Microsoft)
- [Build TypeScript apps with Microsoft Graph](https://learn.microsoft.com/en-us/graph/tutorials/typescript)
- [Install a Microsoft Graph SDK](https://learn.microsoft.com/en-us/graph/sdks/sdk-installation)

## Stav produktu / delta
> [!WARNING]
> Ověřit k datu běhu — viz delta v [`explainer-typescript-graph.md`](explainer-typescript-graph.md)
> (Kiota-based `@microsoft/msgraph-sdk` vs `@microsoft/microsoft-graph-client`).
