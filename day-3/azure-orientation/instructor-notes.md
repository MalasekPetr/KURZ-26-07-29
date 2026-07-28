# Instructor notes — Azure orientace

## Timing

- 45 min výklad + 30 min demo. Blok nemá studentský lab — hands-on Azure by vyžadoval
  per-student resource groups a čas, který D3 nemá; cíl je orientace a odbourání
  respektu, ne provoz.

## Go/no-go

- Přístup do kurzové Azure subscription z instruktorského účtu (subscription je mimo
  M365 Developer tenant billing — viz `environment.md`).
- Demo 2: funkční `docker run mcr.microsoft.com/powershell` na instruktorském stroji
  (image stáhnout předem — učebná síť může být pomalá) nebo připravený devcontainer.
- Demo 3: předpřipravená Function App s managed identity a triviálním skriptem —
  nasadit den předem, na místě jen ukázat.

## Tripwires

- Větu „GA v tenantu ≠ přístup do Azure" zopakovat minimálně dvakrát — je to
  nejčastější aha-moment bloku a vysvětluje, proč účastníci v portálu „nic nevidí".
- Nesklouznout do Azure katalogu služeb — jediné služby, které se smí jmenovat déle
  než větu, jsou Functions, Automation, Storage a Key Vault.
- U kontejnerů nevysvětlovat orchestraci (Kubernetes) — otázky odkázat na přestávku;
  úroveň bloku je image/kontejner/devcontainer.
- Náklady: otázka „kolik to stojí" přijde — mít připravený řádový příklad (Function
  App Consumption plan pro noční sync job = jednotky Kč/měsíc) a odkaz na budget alerty.

## Vazby

- Dopředu: managed identity a „kde běží" tabulka se vrací v [`../capstone-mini/`](../capstone-mini/)
  blueprintu (kam skript nasadit, až kurz skončí).
- Zpět: LocalMachine store a PEM z [`../../day-2/certificates-and-keys/`](../../day-2/certificates-and-keys/);
  žebříček credentials z [`../../day-2/security-hardening/`](../../day-2/security-hardening/);
  runtime prostředí z [`../../day-1/vscode-copilot-env/explainer-runtime-environments.md`](../../day-1/vscode-copilot-env/explainer-runtime-environments.md).
