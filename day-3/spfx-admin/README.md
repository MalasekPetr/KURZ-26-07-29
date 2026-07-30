# Správa SPFx řešení — App Catalog, API access, tenant-wide extensions

> Typ: povinný · Den: 3 · Odhad: 40 min výklad + 15 min demo

Tento blok **není o vývoji** SPFx. Je o tom, co s hotovým `.sppkg` balíčkem dělá
správce: kam ho nasadit, jaká oprávnění mu schválit, jak zjistit, co všechno v tenantu
běží, a jak to udržet pod kontrolou.

## Cíle
- Vědět, co je SPFx řešení z pohledu správce a **v čí identitě běží** (klíčové pro
  bezpečnostní posouzení).
- Umět nasadit a spravovat `.sppkg` v **App Catalogu** — tenant vs. site collection —
  a rozhodnout mezi tenant-wide deploymentem a instalací per web.
- Rozumět **API access** schvalování a jeho největší pasti: oprávnění se udělují
  **jednomu sdílenému service principalu** pro celý tenant.
- Umět dohledat **tenant-wide extensions** — kód, který běží na všech stránkách, aniž
  by ho někdo instaloval.

## Výklad

### Co SPFx je pro správce
SPFx (SharePoint Framework) je **jediná podporovaná cesta customizace** moderního
SharePointu (nástupce Add-inů a JS injection —
[`../../day-1/api-landscape/`](../../day-1/api-landscape/)). Výsledkem vývoje je balíček
`.sppkg`; kód pak běží **v prohlížeči uživatele, pod jeho identitou a s jeho
oprávněními** — nemá vlastní credential jako app registrace. To je dobrá zpráva
(uživatel neuvidí víc, než na co má právo) i varování: **řešení může dělat cokoli,
na co má právo přihlášený uživatel** — u správce tedy hodně.

### App Catalog — dvě úrovně
| Katalog | Kde | Dopad | Kdo spravuje |
|---|---|---|---|
| **Tenant App Catalog** | vyhrazený web (SharePoint admin center → More features → Apps) | řešení dostupné celému tenantu, možnost tenant-wide deploymentu | SharePoint administrator |
| **Site collection app catalog** | jednotlivý web (zapíná se skriptem) | řešení jen na tom webu | vlastník webu |

Site collection katalogy jsou pohodlné pro pilot, ale **rozmělňují přehled** — kód
v tenantu může existovat na místech, kam správce nevidí. V governance pravidlech se
vyplatí je povolovat vědomě, ne jako výchozí stav.

Nasazení: upload `.sppkg` → dialog nabídne **„Make this solution available to all sites
in the organization"** (tenant-wide deployment) → jinak se řešení instaluje per web
(*Site contents → Add an app*). PnP ekvivalenty pro skriptované nasazení:

```powershell
Add-PnPApp -Path .\resenit.sppkg -Scope Tenant -Publish
Get-PnPApp -Scope Tenant | Select-Object Title, AppCatalogVersion, InstalledVersion, Deployed
Install-PnPApp -Identity <app-id> -Scope Tenant       # instalace na aktuální web
Update-PnPApp -Identity <app-id> -Scope Tenant        # po uploadu nové verze
```

### API access — největší past celého bloku
Pokud SPFx řešení potřebuje volat Graph nebo jiné API nad rámec SharePointu, žádá si
oprávnění — a ta se schvalují v **SharePoint admin center → Advanced → API access**.
Tři věci, které je nutné vědět:

1. Schválením se oprávnění přidá **jedinému, celotenantnímu service principalu**
   („SharePoint Online Client Extensibility Web Application Principal"). Není to
   oprávnění „pro tuhle jednu webpart" — **sdílí ho všechna SPFx řešení v tenantu**.
2. Z toho plyne, že schválené `Sites.Read.All` může použít **jakékoli** jiné SPFx
   řešení, které do tenantu přijde později. Least privilege z D2 tady dostává tvrdou
   praktickou lekci: schvalovat jen to nejnutnější a evidovat, kdo o co žádal a proč.
3. Zmírnění existuje: **isolated web parts** (řešení dostane vlastní service principal)
   — u nových požadavků se na ně ptejte dodavatele.

Kontrola stavu: `Get-PnPTenantServicePrincipalPermissionGrants` (co je schválené)
a `Get-PnPTenantServicePrincipalPermissionRequests` (co čeká) — hodí se do pravidelného
auditu vedle app registrací z D2.

### Tenant-wide extensions — kód, který běží všude
Application customizery (např. globální hlavička nebo patička) lze aktivovat pro celý
tenant přes seznam **Tenant Wide Extensions** na webu App Catalogu. Praktický důsledek:
existuje kód, který běží na všech stránkách, **aniž by ho kdokoli instaloval na
konkrétní web**. Když se něco „rozbije všude", tady je první místo, kam se podívat —
a záznam v seznamu lze vypnout jedním přepínačem (`Disabled`).

### Provozní hygiena
- **Verze**: `Get-PnPApp` porovnává `AppCatalogVersion` vs `InstalledVersion` — rozdíl
  znamená, že web běží na staré verzi a čeká na update.
- **Kdo smí nahrávat**: přístup do App Catalogu = právo spustit kód všem uživatelům.
  Patří jen do rukou správců, ne „ať si to tam vývojář hodí sám".
- **Před nasazením žádat**: zdroj (kdo dodal, verze), seznam požadovaných API oprávnění
  se zdůvodněním, a co řešení dělá s daty — stejná otázka jako u app registrace v D2.

## Demo (živě)
1. SharePoint admin center → **Apps** → obsah App Catalogu, detail jednoho řešení
   (verze, tenant-wide vs per-site).
2. **API access** — čekající požadavek a schválené granty; ukázat v Entra, že
   oprávnění visí na **jednom** service principalu pro celý tenant (napojení na
   Enterprise applications z D2).
3. Seznam **Tenant Wide Extensions** na webu App Catalogu — co v tenantu běží globálně.

## Klíčové rozlišení
- **SPFx (běží pod identitou uživatele) vs app registrace (vlastní identita s credentialem)**
  — dvě různé bezpečnostní úvahy; SPFx se neprokazuje certifikátem, dědí práva uživatele.
- **Tenant App Catalog vs site collection app catalog** — přehled a kontrola vs lokální
  pohodlí; druhé zhoršuje viditelnost.
- **Tenant-wide deployment vs instalace per web** — „dostupné všem" vs „vědomě zapnuté
  tam, kde to má být".
- **API access grant (sdílený principal) vs oprávnění app registrace (vlastní principal)**
  — u SPFx schvalujete oprávnění, které pak sdílí všechna řešení v tenantu.

## Tipy
- Před schválením API access požadavku si vyžádejte **písemné zdůvodnění** — je to
  tenant-wide rozhodnutí, které přežije autora řešení.
- `Get-PnPApp -Scope Tenant` + `Get-PnPTenantServicePrincipalPermissionGrants` dejte do
  pravidelného auditního skriptu vedle app registrací (D2 hardening).
- Když „se rozbil SharePoint všem", zkontrolujte Tenant Wide Extensions dřív, než
  začnete hledat výpadek služby.
- SPFx **nepotřebujete umět vyvíjet**, abyste ho uřídili — ale potřebujete umět
  přečíst, co si řešení žádá.

## Zdroje (Microsoft)
- [Use the App Catalog to make custom business apps available](https://learn.microsoft.com/en-us/sharepoint/use-app-catalog)
- [Manage apps using the Apps site](https://learn.microsoft.com/en-us/sharepoint/manage-apps)
- [Connect to Azure AD-secured APIs in SPFx (API access)](https://learn.microsoft.com/en-us/sharepoint/dev/spfx/use-aadhttpclient)
- [Isolated web parts](https://learn.microsoft.com/en-us/sharepoint/dev/spfx/web-parts/isolated-web-parts)
- [Tenant-wide extensions](https://learn.microsoft.com/en-us/sharepoint/dev/spfx/tenant-wide-deployment-extensions)

## Stav produktu / delta
> [!WARNING]
> Ověřit k datu běhu — umístění App Catalogu a API access v SharePoint admin centru se
> po redesignu portálu přesouvá; verze SPFx a podporované scénáře (isolated web parts,
> Graph verze) se mění. Před během proklikat cestu v portálu.
