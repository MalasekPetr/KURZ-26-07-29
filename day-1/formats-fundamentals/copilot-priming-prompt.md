# Priming prompt · Krocení fantazie Copilota pro skriptování

M365 Copilot Chat (free) **nemá trvalé custom instructions** — pravidla se nastavují
tzv. priming promptem: blok níže se vloží jako **první zpráva každé nové konverzace**
a platí pro celý její zbytek. Uložte si ho jako snippet (soubor v repu z Labu D1 /
poznámka) — vkládat budete mnohokrát denně.

Proč: bez mantinelů si Copilot ochotně **vymyslí** neexistující cmdlet, parametr nebo
Graph endpoint — vypadá věrohodně a spadne až za běhu. Priming prompt fantazii omezuje
a hlavně ji **zviditelňuje**: nutí model přiznat nejistotu místo hádání.

Tento dokument je záměrně **ve dvou verzích s testem mezi nimi** — na vlastní oči
uvidíte, jak podoba priming promptu mění výsledek. To je hlavní lekce: prompt není
jednou hotový text, ale živý nástroj, který postupně vylepšujete.

## Verze 1 — základní pravidla (kopírovat celý blok)

```text
Jsi asistent pro PowerShell skriptování nad Microsoft 365 (SharePoint Online,
Microsoft Graph). V celé této konverzaci se řiď těmito pravidly:

1. Používej výhradně existující cmdlety, parametry a API endpointy. Pokud si
   nejsi jistý, že něco existuje, výslovně to napiš a uveď, kde to mám ověřit
   (Get-Command, Get-Help, learn.microsoft.com). Nikdy názvy nevymýšlej.
2. Cílové prostředí: PowerShell 7. Povolené moduly: PnP.PowerShell,
   Microsoft.Graph, Microsoft.Online.SharePoint.PowerShell. Nepoužívej MSOnline
   ani AzureAD (jsou vypnuté) ani jiné moduly, které výslovně nezmíním.
3. Microsoft Graph: endpoint v1.0, ne beta, pokud neřeknu jinak. U každého
   Graph volání uveď, jaké permission vyžaduje.
4. Každý skript: param() s výchozími hodnotami, try/catch s čitelnou chybou,
   u operací, které něco mění nebo mažou, podpora -WhatIf. Výstup vracej jako
   objekty, ne Write-Host.
   Data obsahují českou diakritiku — při čtení/zápisu souborů vždy explicitně
   -Encoding utf8 a CSV pro Excel exportuj s -Encoding utf8BOM -UseCulture.
5. Žádné konkrétní identifikátory: tenant, ClientId, URL, thumbprint piš jako
   placeholder <takto> a na konci vyjmenuj, čím je mám nahradit.
6. Když je moje zadání nejednoznačné, polož doplňující otázku — nehádej.
7. Když něco nevíš, napiš „nejsem si jistý" a navrhni způsob ověření. Jistotu
   nepředstírej.
8. Ke každému skriptu přidej: co dělá (max 2 věty), jaká oprávnění potřebuje
   a jak ho bezpečně otestovat před ostrým spuštěním.
```

## Test verze 1 — co chytí a co spolehlivě propustí

**Test A — návnada (chytí):** *„Použij cmdlet `Get-PnPSiteHealthScore` a ukaž mi
příklad."* Cmdlet neexistuje — správná reakce je pochybnost/ověření dle pravidla 1.
Pokud model přesto vyrobí sebevědomý příklad, vidíte fantazii v čisté podobě.

**Test B — připojení k SPO (spolehlivě selže):** *„Napiš skript, který se přihlásí
k webu přes PnP.PowerShell a vypíše všechny seznamy."* Typický návrh s verzí 1:

```powershell
Connect-PnPOnline -Url "https://<tenant>.sharepoint.com/sites/<web>" -Interactive
```

Vypadá správně — a **při spuštění spadne** (chyba typu `AADSTS700016` / „Specify a
valid client id"). Proč: PnP.PowerShell od 9. 9. 2024 **vyžaduje vlastní `-ClientId`**
(sdílené výchozí bylo odebráno), jenže internet je plný let starých příkladů bez něj —
a model generuje nejčastější vzor, který zná. Pravidlo 1 tady nepomůže: cmdlet i
parametry **existují**, jen je jejich kombinace zastaralá. Druhá tvář fantazie:
nejen vymyšlené názvy, ale i **reálná, jen neaktuální praxe**.

## Verze 2 — doplněný odstavec

Do bloku výše přidejte deváté pravidlo (úplná verze 2 = verze 1 + tento odstavec):

```text
9. Připojení k SharePoint Online: vždy Connect-PnPOnline s explicitním
   -ClientId <moje app registrace> (a -Tenant u app-only). Auth módy:
   -Interactive (delegated), -UseDeviceLogin, nebo -Thumbprint <thumbprint>
   (app-only, bez promptu). PnP.PowerShell od září 2024 nemá výchozí ClientId —
   nikdy negeneruj Connect-PnPOnline bez -ClientId a nepoužívej zastaralé
   -UseWebLogin ani -Credentials.
```

**Test B podruhé** (nová konverzace, verze 2): návrh teď obsahuje
`-ClientId <client-id-app-registrace>` jako placeholder dle pravidla 5, správný auth
mód a poznámku, čím placeholder nahradit. Stejný model, stejná otázka — jiný prompt,
jiný (funkční) výsledek.

## Pointa — prompt je živý dokument

Workflow celého kurzu: když Copilot **opakovaně** dělá tutéž chybu, nepřepisujte
pořád jeho výstup — **přidejte pravidlo do priming promptu**. Verzi promptu držte
v repu z Labu D1 vedle skriptů (je to taky kód). Během D2–D3 vám přibudou vlastní
pravidla (interní názvy polí do promptu, stránkování, …).

**Evoluce → agent (D4/D5):** vkládat prompt do každé konverzace je daň za free verzi.
V D4 ([`../../day-4/agent-builder/`](../../day-4/agent-builder/)) si z finální verze
promptu postavíte **deklarativního agenta „Scripting Assistant"** — pravidla se
stanou trvalými instrukcemi agenta a vkládání končí. Přesně tahle cesta
(prompt → otestovaná pravidla → agent) je hlavní AI dovednost, kterou si z kurzu
odnášíte.

## Vazby

- Verze 1 + Test A se vkládá v Labu 1 ([`lab-first-script.md`](lab-first-script.md),
  krok 3); Test B a verze 2 živě v D2 Labu 3
  ([`../../day-2/powershell-deep-dive/lab-cert-auth-sites.md`](../../day-2/powershell-deep-dive/lab-cert-auth-sites.md)) —
  tam se poprvé připojujete.
- Pravidla 4–5 kopírují review checklist z [`../vscode-copilot-env/`](../vscode-copilot-env/)
  a zákaz citlivých identifikátorů z [`../onboarding/ways-of-working.md`](../onboarding/ways-of-working.md).
- Pravidlo 9 odpovídá deltě v [`../../day-2/powershell-deep-dive/README.md`](../../day-2/powershell-deep-dive/README.md)
  (PnP ClientId změna 9/2024).

> [!WARNING]
> Ověřit k datu běhu — chování modelů se mění: Test B projeďte den předem; pokud už
> model generuje `-ClientId` sám od sebe, máte slabší demo (a lepší svět) — pak
> demonstrujte na jiném spolehlivém failu, princip verze 1 → test → verze 2 platí
> dál. Pokud mezitím M365 Copilot Chat dostane trvalé instrukce/paměť, přenést
> obsah tam.
