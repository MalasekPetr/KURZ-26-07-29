# Prostředí kurzu — pracovní tenanty a Azure

Referenční údaje o prostředí, na které se odkazují laby. Kurz kombinuje dva světy
s různými nároky: dny 1–3 (API & skriptování) potřebují **Global admin práva** a pro
instruktorská dema **Azure subscription**, dny 4–5 (AI) potřebují **Copilot Credits
PAYG** (případně M365 Copilot licence).

> [!IMPORTANT] Publikace
> Tento soubor obsahuje jen student-facing část. Instructor-only údaje (tenant ID,
> admin účet, jmenný seznam účastníků, app registrace se secrety/certifikáty, Azure
> subscription ID) jsou drženy mimo repo, v instruktorském kanálu.

> [!WARNING] Rozhodnout před během — jeden tenant, nebo dva?
> GOC223 běží na M365 **E5 Developer** tenantu (`cloudedu.cz`, studenti = Global admin),
> GOC224 na tenantu `spdemo.online` (Business Basic + **Copilot Credits PAYG**).
> Pro tento kombinovaný běh preferovat **jeden tenant pro celý týden**: E5 Developer
> tenant s aktivovaným PAYG billing (Azure subscription + resource group), aby D4–5
> navázaly na weby a artefakty z D1–3 (capstone je propojuje). Fallback = přepnutí
> tenantů mezi D3 a D4 (model GOC224 níže) — pak ale agenti v D4–5 groundují nad
> předpřipraveným obsahem, ne nad výstupy migračního labu. Ověřit dostupnost
> Copilot PAYG na Developer tenantu minimálně 2 týdny před během.

## Student-facing — M365 tenant (dny 1–3, primární)

| Položka | Hodnota |
|---|---|
| Tenant | M365 Developer tenant (Microsoft 365 Developer Program) |
| Přihlašovací doména | `cloudedu.cz` |
| Účty studentů | `jmeno.prijmeni@cloudedu.cz` (bez diakritiky) |
| Licence | Microsoft 365 **E5 Developer** |
| Role | **Global administrator** — všichni studenti (viz [`day-1/onboarding/ways-of-working.md`](day-1/onboarding/ways-of-working.md)) |
| Hesla | přidělena na začátku kurzu, MFA povinné při prvním přihlášení |

> [!NOTE] Všichni studenti jsou Global administrátoři jednoho sdíleného tenantu —
> izolaci nezajišťují role, ale pravidla a naming konvence (`ways-of-working.md`).
> Po kurzu se obsah čistí offboarding skripty.

> [!WARNING] Ověřit k datu běhu.
> M365 Developer Program byl v roce 2024 omezen (nové sandboxy jen pro vybrané
> subscribery, obnova tenantu závisí na aktivitě). Ověřit životnost tenantu
> minimálně týden před během — náhrada se nedá zajistit přes noc.

## Student-facing — AI/Copilot (dny 4–5)

| Položka | Hodnota |
|---|---|
| AI | Copilot přes **Pay-as-you-go** (Copilot Credits) — nastavit na kurzovém tenantu |
| Fallback tenant | `spdemo.online` (model GOC224: Business Basic + PAYG, root `https://ms365x17157302.sharepoint.com`) |

### Náklady — PAYG upozornění pro učebnu
> [!WARNING] Ověřit k datu běhu.
> Copilot Credits ($0,01/kredit) čerpá **každá reálná** interakce (grounding,
> generative answer, tool call) — orientačně ~12 kreditů/konverzace. **Žádný tvrdý
> strop** (alerty nezastaví spotřebu). Mít nastavený budget alert a hlídat opakované
> spouštění. Tvorba SharePoint agenta vyžaduje Copilot **licenci** (PAYG nestačí) —
> proto je blok `day-5/sharepoint-agents` instruktorské demo.

## Azure (den 3 — jen instruktorská dema)

M365 Developer tenant **neobsahuje** Azure subscription — dema D3
(`day-3/azure-orientation`) běží nad samostatnou, placenou subscription připojenou
k témuž tenantu. **Revize 2026-07-28: studenti Azure přístup nepotřebují** (Azure laby
přesunuty do `backlog/`) — subscription slouží jen instruktorovi.

| Položka | Hodnota |
|---|---|
| Azure subscription | dedikovaná kurzová subscription (instruktorský kanál) |
| Demo resource group | `rg-kuzk-demo` — IAM ukázka, Function App s managed identity |
| Rozsah | Function App (Consumption plan) + Storage Account pro demo 3 |
| Role studentů | žádná — portál se jen promítá |

> [!NOTE]
> Global administrator v tenantu ≠ přístup k Azure — Entra role a Azure RBAC
> jsou oddělené soustavy (teaching point `day-3/azure-orientation`).

> [!WARNING]
> Ověřit k datu běhu — stav k 2026-07. Demo Function App nasadit den předem a po kurzu
> uklidit; mít budget alert na subscription. Tatáž subscription slouží jako billing
> plumbing pro Copilot Credits PAYG (D4–5) — zřídit jednou, použít pro obojí.

## Instruktorský hardware (den 2 — YubiKey demo)

| Položka | Hodnota |
|---|---|
| YubiKey | min. 2× YubiKey 5 (PIV) — jeden demo, jeden na kolečko mezi účastníky |
| Software | YubiKey Manager CLI (`winget install Yubico.YubiKeyManagerCLI`) + Smart Card Minidriver (`winget install Yubico.YubiKeySmartCardMinidriver`) |
| Příprava | změněné výchozí PIV PIN/PUK/management key; generálka dema den předem (`day-2/certificates-and-keys/demo-yubikey.md`) |
| Účastníci | instalace není potřeba; volitelně dle [`day-2/certificates-and-keys/setup-ykman.md`](day-2/certificates-and-keys/setup-ykman.md) (vlastní YubiKey) |

## Student-facing — vývojářské nástroje

| Položka | Hodnota |
|---|---|
| VS Code | poslední stabilní verze, extension pack: PowerShell, Microsoft 365 Agents Toolkit (D5) |
| PowerShell | PowerShell 7 (aktuální LTS) vedle Windows PowerShell 5.1 |
| AI asistent | **M365 Copilot Chat (free)** — součást přihlášení kurzovním účtem (`m365.cloud.microsoft/chat`), enterprise data protection; **nic se nedokupuje** |
| Git | nainstalovaný, nakonfigurovaný `user.name`/`user.email` před D1 |

> [!WARNING]
> Ověřit k datu běhu. Revize 2026-07-28: kurz jede na **free** M365 Copilot Chat
> (žádné GitHub Copilot licence). Go/no-go: den před D1 ověřit na **studentském** účtu
> `cloudedu.cz`, že se Copilot Chat otevře a ukazuje enterprise data protection (ikona
> štítu). GitHub Copilot (placený/free tier) se jen zmiňuje jako navazující krok.
