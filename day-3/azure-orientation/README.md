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
| Kontejner (CI/CD, cloud) | přenositelnost, izolace | cert/federated credentials, soubory PEM (D2) |

Pointa: čím výš na žebříku, tím **méně** tajemství leží na discích —
managed identity nemá co ukrást. Azure tu není povinnost, ale odměna.

### Kontejnery — přenositelné prostředí za 5 minut
Kontejner = zabalený běhový svět (OS knihovny + runtime + nástroje), který se všude
spustí stejně. Image `mcr.microsoft.com/powershell` obsahuje hotový PowerShell 7 na
Linuxu — tentýž skript z labů běží v něm beze změny. **Devcontainer** (`devcontainer.json`
v repu) dá celému týmu identické vývojové prostředí ve VS Code — konec „u mě to funguje".
Kde se to potká s M365: CI/CD pipeline (YAML z D1!) spouští skripty právě v kontejnerech.

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
3. **Devcontainer** — [`.devcontainer/devcontainer.json`](../../.devcontainer/devcontainer.json)
   tohoto repa: otevřít repo v **GitHub Codespaces** (*Code → Codespaces → Create*) a
   ukázat, že celý tým dostane identické prostředí (PowerShell 7 + PnP + extensions)
   definované jedním JSON souborem v gitu — „u mě to funguje" končí. Lokálně totéž dělá
   *Dev Containers: Reopen in Container* ve VS Code (vyžaduje Docker/Podman).
4. Azure Function s managed identity (předpřipravená): tělo skriptu bez jediného
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

## Tipy
- **Kontejner si osaháte bez instalace**: Cloud Shell (`shell.azure.com`) i Codespaces
  běží v prohlížeči. Lokální runtime (Docker Desktop / Podman) potřebujete až tehdy,
  když chcete kontejnery provozovat, ne poznat.
- Na Windows vyžadují Docker Desktop i Podman **WSL2** — instalace znamená restart
  stroje; nepouštějte se do ní pět minut před tím, než to potřebujete.
- **Licenční pozor u Docker Desktop**: pro větší organizace je placený. Podman je
  bez tohoto omezení a příkazy jsou stejné (`podman run …` místo `docker run …`).
- Kdo chce kontejnery vyzkoušet doma: `wsl --install` (restart) →
  `winget install RedHat.Podman` → `podman machine init && podman machine start` →
  `podman run -it mcr.microsoft.com/powershell`.

## Stav produktu / delta
> [!WARNING]
> Ověřit k datu běhu — vzhled a chování Azure Cloud Shell, dostupnost Codespaces
> (free tier hodin) a licenční podmínky Docker Desktopu se mění. Demo 2 a 3 projít
> den předem; obojí vyžaduje jen prohlížeč a přihlášení.
