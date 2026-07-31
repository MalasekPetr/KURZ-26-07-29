# Azure orientace: subscription, RBAC, kde skript běží

> Typ: povinný · Den: 3 · Odhad: 30 min výklad + 15 min živé demo (při skluzu dne první ke zkrácení)

## Cíle
- Orientovat se v základních stavebních kamenech Azure: tenant vs subscription vs
  resource group vs resource — a proč **Global admin v M365 ≠ přístup do Azure**.
- Rozumět Azure RBAC (role na scope) a rozdílu proti Entra rolím a M365 licencím.
- Umět zařadit, **kde automatizační skript může běžet**: vlastní stanice → scheduled
  task na serveru → Azure (Functions/Automation) → kontejner — a co která volba znamená
  pro credentials.
- Vědět, co je kontejner a k čemu je dobrý devcontainer — úroveň „poznám a zařadím",
  ne „provozuji".

## Výklad

### Tenant vs subscription — dvě soustavy, jeden účet
**Entra tenant** je adresář identit (uživatelé, skupiny, aplikace — celý D2). **Azure
subscription** je fakturační a provozní kontejner pro cloudové zdroje, *připojený*
k tenantu. Z toho plyne věta, která šetří hodiny zmatku: **Global administrator v tenantu
nemá automaticky žádný přístup k Azure** — Entra role a Azure RBAC jsou oddělené soustavy.
Hierarchie: subscription → **resource group** (logická krabice na související zdroje,
jednotka úklidu a účtování) → **resource** (Function App, Storage Account, Key Vault…).

### Azure RBAC — role na scope
Přístup v Azure = **role** (co smím: Reader/Contributor/Owner nebo jemnější) přiřazená
na **scope** (kde to smím: subscription / resource group / jednotlivý resource).
Least-privilege princip z D2 platí beze změny — jen se místo API permissions přiřazují
role na co nejužší scope. Praktický vzor z tohoto kurzu: účastník = Contributor jen na
vlastní resource group, ne na subscription (viz [`../../environment.md`](../../environment.md)).

### Kde skript běží — žebřík dospělosti automatizace
| Kde | Kdy stačí | Credentials |
|---|---|---|
| Vlastní stanice, ručně | ad-hoc, vývoj | interactive/device code (delegated) |
| Scheduled task na serveru | pravidelný běh on-prem | certifikát v **LocalMachine** store (D2) |
| Azure Functions / Automation | pravidelný běh bez vlastního železa | **managed identity** — žádný spravovaný secret |
| **Container Instances (ACI)** | jednorázový/dávkový běh, „spusť a zapomeň" | managed identity, nebo PEM/federated credentials |
| **Container Apps Job** | plánovaný běh v kontejneru, scale-to-zero | managed identity |
| CI/CD pipeline (kontejner) | build, test, nasazení skriptů | federated credentials / cert v Key Vaultu |

Pointa: čím výš na žebříku, tím **méně** tajemství leží na discích —
managed identity nemá co ukrást. Azure tu není povinnost, ale odměna.

### Kontejnery — přenositelné prostředí za 5 minut
Kontejner = zabalený běhový svět (OS knihovny + runtime + nástroje), který se všude
spustí stejně. Image `mcr.microsoft.com/powershell` obsahuje hotový PowerShell 7 na
Linuxu — tentýž skript z labů běží v něm beze změny. **Devcontainer** (`devcontainer.json`
v repu) dá celému týmu identické vývojové prostředí ve VS Code — konec „u mě to funguje".
Kde se to potká s M365: CI/CD pipeline (YAML z D1!) spouští skripty právě v kontejnerech.

**Kontejnery si nemusíte provozovat, aby vám běžely** — v Azure jsou to hotové služby
a nepotřebujete na svém stroji vůbec nic:

| Služba | K čemu | Poznámka |
|---|---|---|
| **Cloud Shell** | konzole v prohlížeči | sama je kontejner s PS7; efemérní, přežije jen `$HOME` |
| **Container Instances (ACI)** | „spusť tenhle image, vypiš log, zmiz" | jeden příkaz, platí se po sekundách |
| **Container Apps Job** | plánovaný běh (cron), scale-to-zero | dospělá varianta ACI pro opakované úlohy |
| **Container Registry (ACR)** | kde bydlí *váš* image | až když si obraz stavíte sami |

Lokální Docker/Podman potřebujete teprve tehdy, když chcete image **stavět** — pro
spouštění stačí Azure. (Pro tuto skupinu je to podstatná zpráva: žádná instalace,
žádná licence Docker Desktopu, žádný restart stroje.)

## Demo (živě)
1. Portál: subscription → kurzová resource group → přiřazené role (IAM) — ukázat, že
   GA účet bez role subscription nevidí.
2. **Kontejner bez instalace čehokoli — Azure Cloud Shell** (`shell.azure.com`) je sám
   kontejner s PowerShellem 7 na Linuxu:

   ```powershell
   $PSVersionTable            # PowerShell 7 … na Linuxu
   cat /etc/os-release        # jsme v Linux kontejneru, ne na svém Windows
   Get-ChildItem Cert:\       # SELŽE — Cert: provider na Linuxu neexistuje → proto PEM (D2)
   Install-Module PnP.PowerShell -Scope CurrentUser -Force
   ```

   Pointa: session je **efemérní** — zavřením zmizí, přežije jen `$HOME` (file share).
   Kontejner = zabalené běhové prostředí, ne váš počítač.
3. **Vlastní kontejner v Azure — Container Instances** (jádro dema): spustit PowerShell
   skript v kontejneru na jeden příkaz, přečíst log, uklidit. Bez lokálního Dockeru.

   ```powershell
   $rg = "rg-kuzk-demo"
   az group create -n $rg -l westeurope                     # jednou (vyhrazená RG, ne produkční!)

   az container create -g $rg -n kurz-pwsh `
     --image mcr.microsoft.com/powershell:latest `
     --os-type Linux --cpu 1 --memory 1 --restart-policy Never `
     --command-line 'pwsh -NoProfile -Command "$PSVersionTable.PSVersion.ToString(); Get-Content /etc/os-release -TotalCount 2"'

   az container logs -g $rg -n kurz-pwsh                    # výstup skriptu z kontejneru
   az container show  -g $rg -n kurz-pwsh --query "instanceView.state" -o tsv
   az container delete -g $rg -n kurz-pwsh --yes             # úklid — platí se po sekundách
   ```

   Pointa: **stejný skript, žádná instalace, žádný server** — jen deklarace „tento image,
   tento příkaz". `--restart-policy Never` = dávková úloha, ne služba.
   Nástavba k probranému žebříčku: `--assign-identity <resourceId uživatelské MI>` dá
   kontejneru **managed identitu** — a pak v něm neběží žádný secret ani certifikát.
4. **Devcontainer** (krátce, jen když je čas) —
   [`.devcontainer/devcontainer.json`](../../.devcontainer/devcontainer.json) tohoto repa
   otevřený v **GitHub Codespaces**: celý tým dostane identické prostředí (PowerShell 7 +
   PnP + extensions) definované jedním JSON souborem v gitu.
5. Azure Function s managed identity (předpřipravená): tělo skriptu bez jediného
   credentialu — kam se firma může dostat, až workflow zapustí kořeny.

## Klíčové rozlišení
- **Entra role vs Azure RBAC vs M365 licence** — tři oddělené soustavy; GA ≠ Azure
  přístup, licence ≠ oprávnění (nit z [`../../day-1/onboarding/ways-of-working.md`](../../day-1/onboarding/ways-of-working.md)).
- **Resource group jako jednotka úklidu** — co vznikne spolu, ať zmizí spolu; základ
  nákladové hygieny.
- **Managed identity vs certifikát** — obojí app-only (D2); managed identity jen na
  Azure resourcech, zato bez čehokoli, co by šlo ukrást nebo zapomenout zrotovat.
- **Kontejner vs VM/server** — kontejner nese jen běhové prostředí procesu, ne celý
  operační systém; startuje v sekundách a je definovaný souborem v repu.

## Zdroje (Microsoft)
- [Azure fundamental concepts (Cloud Adoption Framework)](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/considerations/fundamental-concepts)
- [What is Azure role-based access control (RBAC)?](https://learn.microsoft.com/en-us/azure/role-based-access-control/overview)
- [Managed identities for Azure resources](https://learn.microsoft.com/en-us/entra/identity/managed-identities-azure-resources/overview)
- [Developing inside a Container (VS Code)](https://code.visualstudio.com/docs/devcontainers/containers)
- [Azure Container Instances — quickstart (Azure CLI)](https://learn.microsoft.com/en-us/azure/container-instances/container-instances-quickstart)
- [Container Apps Jobs — spouštění dávkových úloh](https://learn.microsoft.com/en-us/azure/container-apps/jobs)
- [Managed identity v Azure Container Instances](https://learn.microsoft.com/en-us/azure/container-instances/container-instances-managed-identity)

## Tipy
- **Kontejnery zkoušejte v Azure, ne na svém stroji**: Cloud Shell a ACI zvládnete
  z prohlížeče a Az CLI. Lokální runtime řešte teprve, až budete image **stavět**.
- **ACI vždy uklidit** (`az container delete`) — účtuje se po sekundách, ale zapomenutý
  kontejner s `--restart-policy Always` běží věčně. Vyhrazená resource group na dema
  je pravidlo, ne kosmetika: co vznikne spolu, ať zmizí spolu.
- Dema **nikdy do produkční resource group** — i „jen na chvíli". Vlastní `rg-*-demo`
  a po akci `az group delete`.
- Na Windows vyžadují Docker Desktop i Podman **WSL2** — instalace znamená restart
  stroje; nepouštějte se do ní pět minut před tím, než to potřebujete.
- **Licenční pozor u Docker Desktop**: pro větší organizace je placený. Podman je
  bez tohoto omezení a příkazy jsou stejné (`podman run …` místo `docker run …`).
- Kdo chce kontejnery lokálně: `wsl --install` (restart) →
  `winget install RedHat.Podman` → `podman machine init && podman machine start` →
  `podman run -it mcr.microsoft.com/powershell`.

## Stav produktu / delta
> [!WARNING]
> Ověřit k datu běhu — vzhled a chování Azure Cloud Shell, syntaxe `az container`
> (a podpora managed identity v ACI), dostupnost Codespaces free tier a licenční
> podmínky Docker Desktopu se mění. Dema 2 a 3 projít den předem — ACI kontejner
> nastartuje 20–60 s, s tím ve výkladu počítat (a mít připravený log z generálky).
