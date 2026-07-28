# Azure orientace: subscription, RBAC, kde skript běží

> Typ: povinný · Den: 3 · Odhad: 45 min výklad + 30 min instruktorské demo

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

Pointa pro opatrnou firmu: čím výš na žebříku, tím **méně** tajemství leží na discích —
managed identity nemá co ukrást. Azure tu není povinnost, ale odměna.

### Kontejnery — přenositelné prostředí za 5 minut
Kontejner = zabalený běhový svět (OS knihovny + runtime + nástroje), který se všude
spustí stejně. Image `mcr.microsoft.com/powershell` obsahuje hotový PowerShell 7 na
Linuxu — tentýž skript z labů běží v něm beze změny. **Devcontainer** (`devcontainer.json`
v repu) dá celému týmu identické vývojové prostředí ve VS Code — konec „u mě to funguje".
Kde se to potká s M365: CI/CD pipeline (YAML z D1!) spouští skripty právě v kontejnerech.

## Demo (instruktor)
1. Portál: subscription → kurzová resource group → přiřazené role (IAM) — ukázat, že
   GA účet bez role subscription nevidí.
2. `docker run -it mcr.microsoft.com/powershell` (nebo devcontainer ve VS Code):
   spustit skript z Labu 1 uvnitř kontejneru — stejný výstup, jiný svět. Ukázat, že
   `Cert:\` drive v Linux kontejneru neexistuje → vazba na PEM z D2.
3. Azure Function s managed identity (předpřipravená): tělo skriptu bez jediného
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

## Stav produktu / delta
> [!WARNING]
> Ověřit k datu běhu — dostupnost Docker Desktop / Podman na učebních strojích pro
> demo 2 (licencování Docker Desktop ve firmách se mění); fallback je devcontainer
> v GitHub Codespaces nebo jen promítnuté demo.
