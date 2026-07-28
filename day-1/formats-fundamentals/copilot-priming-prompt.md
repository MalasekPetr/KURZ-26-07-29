# Priming prompt · Krocení fantazie Copilota pro skriptování

M365 Copilot Chat (free) **nemá trvalé custom instructions** — pravidla se nastavují
tzv. priming promptem: blok níže se vloží jako **první zpráva každé nové konverzace**
a platí pro celý její zbytek. Uložte si ho jako snippet (soubor v repu z Labu D1 /
poznámka) — vkládat budete mnohokrát denně.

Proč: bez mantinelů si Copilot ochotně **vymyslí** neexistující cmdlet, parametr nebo
Graph endpoint — vypadá věrohodně a spadne až za běhu. Priming prompt fantazii omezuje
a hlavně ji **zviditelňuje**: nutí model přiznat nejistotu místo hádání.

## Prompt (kopírovat celý)

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

## Test, že prompt funguje (součást cvičení)

Po vložení primingu položte návnadu: *„Použij cmdlet `Get-PnPSiteHealthScore` a ukaž
mi příklad."* (cmdlet neexistuje). Správná reakce Copilota je pochybnost/ověření dle
pravidla 1 — pokud místo toho vyrobí sebevědomý příklad, máte před sebou přesně tu
fantazii, kterou celý kurz učí chytat: **model pravidla zlepšují, ale nezaručují;
poslední kontrola je vždy člověk + `Get-Command` + test.**

## Vazby

- Vkládá se v Labu 1 ([`lab-first-script.md`](lab-first-script.md), krok 3) a dál
  v každé nové konverzaci po celý kurz.
- Pravidla 4–5 kopírují review checklist z [`../vscode-copilot-env/`](../vscode-copilot-env/)
  a zákaz citlivých identifikátorů z [`../onboarding/ways-of-working.md`](../onboarding/ways-of-working.md).

> [!WARNING]
> Ověřit k datu běhu — pokud mezitím M365 Copilot Chat dostane trvalé
> instrukce/paměť (nebo firma přejde na GitHub Copilot s `copilot-instructions.md`),
> přenést obsah tam a tento postup zjednodušit.
