# Security hardening & least privilege

> Typ: povinný · Den: 2 (závěr dne) · Odhad: 40 min výklad + 60 min lab

## Cíle
- Minimalizace záběru, dopad Conditional Access.
- Rotace tajemství a přechod na cert-based auth.

## Výklad

### Minimalizace záběru — audit permissions
Projít permissions přiřazené app registraci během dnešních labů (Lab 2: delegated,
Lab 3: application `Sites.FullControl.All`) a odebrat vše, co reálně nebylo použito nebo
má přesnější (užší) alternativu — stejný princip jako v [`../automation-strategy/`](../automation-strategy/),
teď aplikovaný na skutečnou historii použití, ne na odhad předem. V praxi je
tohle klíčový rituál: oprávnění se dají kdykoli zpětně zúžit a doložit auditem.

### Conditional Access pro workload identities (service principals)
Conditional Access lze cílit i na service principaly (workload identities), ne jen na
uživatele — ale s důležitými omezeními: politika musí být **přiřazená přímo** konkrétnímu
service principalu, přiřazení přes skupinu, do které service principal patří, se
**nevynucuje**. Multitenant Microsoft/3rd-party SaaS aplikace a **managed identity nejsou
touto politikou pokryté vůbec**. Vyžaduje licenci Workload Identities Premium. Continuous
Access Evaluation (CAE) pro workload identity umí vynutit odvolání tokenu v reálném čase —
kompromitovaný service principal lze takto odříznout bez čekání na expiraci tokenu.

### Rotace tajemství a cert-based auth
Aktuální doporučené pořadí preferencí pro produkční workload identitu: **managed identity**
(Azure sama řeší rotaci a úložiště — nejlepší volba, kde je aplikovatelná) → **certifikát**
(asymetrický klíč, odolnější proti exfiltraci, vhodný mimo Azure nebo tam, kde managed
identity nejde použít) → **client secret** (jen pro vývoj/test, v produkci se má
odstranit). Doporučená maximální životnost certifikátu je **180 dní** — automatizovat
rotaci přes Azure Key Vault. `keyCredentials` na app registraci je multi-hodnotové pole —
lze mít nahraný starý i nový certifikát současně a provést rotaci bez výpadku (nejdřív
nahrát nový, přepnout klienty, pak teprve odebrat starý).

```mermaid
flowchart LR
  A[Managed identity] -->|není aplikovatelné mimo Azure| B[Certifikát, max 180 dní]
  B -->|jen dev/test| C[Client secret]
  D[Rotace bez výpadku] --> E[Nahrát nový cert] --> F[Přepnout klienty] --> G[Odebrat starý cert]
```

## Klíčové rozlišení
- **Managed identity vs certifikát vs client secret** — v tomto pořadí preference pro
  produkci; secret nikdy není produkční volba.
- **Conditional Access přiřazená přímo service principalu vs přes skupinu** — jen první
  varianta se reálně vynucuje.
- **CAE (real-time token revocation) vs čekání na přirozenou expiraci tokenu** — CAE odřízne
  kompromitovanou identitu okamžitě.

## Lab
Viz [`lab-hardening-app-registration.md`](lab-hardening-app-registration.md).

## Tipy
- Pořadí rotace je zákon: **nahrát nový → ověřit připojení → teprve pak smazat starý**.
  Obrácené pořadí = výpadek, přesně tomu se `keyCredentials` overlap vyhýbá.
- Conditional Access pro service principal se vynucuje jen při **přímém** přiřazení —
  přiřazení přes skupinu tiše nefunguje (na rozdíl od CA pro uživatele).
- Zdůvodnění permissions si zapisujte průběžně (od Labu 2) — audit na konci dne je pak
  čtení poznámek, ne archeologie.

## Zdroje (Microsoft)
- [Microsoft Entra Conditional Access for workload identities](https://learn.microsoft.com/en-us/entra/identity/conditional-access/workload-identity)
- [Migrate applications away from secret-based authentication](https://learn.microsoft.com/en-us/entra/identity/enterprise-apps/migrate-applications-from-secrets)
- [Security best practices for application properties](https://learn.microsoft.com/en-us/entra/identity-platform/security-best-practices-for-app-registration)

## Stav produktu / delta
- Ověřit k datu běhu — doporučená maximální životnost certifikátu (180 dní) a licenční
  požadavek Workload Identities Premium pro Conditional Access na service principaly se
  mohou zpřesnit; ověřit aktuální hodnoty na [Conditional Access for workload identities](https://learn.microsoft.com/en-us/entra/identity/conditional-access/workload-identity) před přípravou labu.
