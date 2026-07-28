# Výklad · AI subprocesoři v Microsoft 365 Copilot (Anthropic, EU Data Boundary)

> Modul: copilot-admin · Doplněk k sekci „AI providers" v CCS · Governance/compliance téma (ISO 27001, datová suverenita)
> Prostředí: viz [`../../environment.md`](../../environment.md) · Názvosloví: [`../../GLOSSARY.md`](../../GLOSSARY.md)

Microsoft 365 Copilot může vedle vlastních (OpenAI) modelů používat **modely třetích stran**. Pro compliance je klíčové vědět **kdo data zpracovává, kde, a zda to opouští Microsoft boundary.**

## Anthropic jako Microsoft subprocesor

- Anthropic je **onboardovaný Microsoft subprocesor** — běžné modely spadají pod **Microsoft Product Terms + DPA** (Enterprise Data Protection, Customer Copyright Commitment platí).
- Plochy, kde se Anthropic modely *můžou* použít po zapnutí: **Microsoft 365 Copilot (apps + chat), Researcher, Copilot Studio, Power Platform, Copilot in Microsoft 365 apps, SharePoint AI capabilities**. Konkrétní ukázky: Researcher, Copilot v Excelu (Edit), Copilot Cowork, výběr modelu v Copilot Studiu.
- **UI indikátor**: v Copilotu je vidět, když zrovna běží Claude.

## Kde se to zpracovává (nosné pro suverenitu)

> [!WARNING] Data opouštějí Microsoft boundary
> Anthropic inference **neběží na Azure** — data jdou z Azure na **Anthropic infra (AWS/GCP), převážně v USA**, a jsou **vyřazena z EU Data Boundary** i in-country závazků. Když Anthropic zapnete, porušíte „data zůstávají v Microsoft/Azure" — flagovat u zákazníků.

## Default podle regionu

- **EU / EFTA / UK: VYPNUTO ve výchozím stavu** (opt-in). → naše české tenanty default off.
- Zbytek komerčního cloudu: on-by-default. GCC/GCC High/DoD a další sovereign cloudy: Anthropic nedostupný (federal GCC/High/DoD), non-federal GCC opt-in od 15. 7. 2026.

## Subprocesor vs. nezávislý procesor

| | Kdo zpracovává | Terms | Retence | Default |
|---|---|---|---|---|
| **Běžné Anthropic modely** | Microsoft **subprocesor** | Microsoft DPA/Product Terms | dle Microsoft | dle regionu (EU off) |
| **Preview models with Data Retention** (Claude Fable 5, Mythos 5) | Anthropic jako **nezávislý procesor** | **Anthropic** Commercial Terms + DPA | Anthropic drží data (30 dní; flagovaný obsah až 2 roky / skóre 7 let) | **vždy off, jen explicitní opt-in** |

Pro citlivá zákaznická data = Preview models with Data Retention **tvrdé ne** bez samostatného posouzení a souhlasu.

## Kde se to ovládá

M365 admin center → **Copilot → Settings → „AI providers operating as Microsoft subprocessors"** → zapnout/vypnout Anthropic, přiřadit per uživatel/skupina. Role **AI Administrator** nebo Global Admin. Pro Copilot Studio / Power Platform navíc kontrola v **Power Platform admin centru**.

## A Skills / Copilot in SharePoint?

**Copilot in SharePoint běží na Microsoft-managed modelu od OpenAI, model se nekonfiguruje** ([get-started](https://learn.microsoft.com/en-us/sharepoint/copilot-in-sharepoint-get-started#models)). Skills tedy **na Anthropicu nezávisí** — v této cestě se Claude aktuálně nepoužívá. Anthropic je věc *jiných* Copilot ploch a je řízený výše uvedeným přepínačem.

## Pro ISO 27001 / zákaznická review

- Ověřit stav přepínače „AI providers" v tenantu zákazníka (EU = default off, ale ověřit).
- Zapnutí Anthropicu = **transfer mimo EUDB** → dokumentovat, získat souhlas, zvážit dopad na registr zpracování.
- Preview models with Data Retention držet vypnuté, pokud není samostatně schváleno.

## Zdroje (Microsoft)

[Anthropic models in Microsoft Online Services](https://learn.microsoft.com/en-us/microsoft-365/copilot/connect-to-ai-subprocessor) · [Overview of AI Subprocessors](https://learn.microsoft.com/en-us/microsoft-365/copilot/copilot-subprocessor-overview) · [Copilot in M365 apps with Anthropic models](https://learn.microsoft.com/en-us/microsoft-365/copilot/copilot-anthropic-apps) · [Microsoft Online Services Subprocessors List](https://aka.ms/subprocessor)

## Stav produktu / delta

> [!WARNING] Ověřit k datu běhu — stav k 2026-07.
> Default-on rollout mimo EU/UK a data se hýbou; docs `connect-to-ai-subprocessor` (ms.date 2026-07) ověřit. Model Copilot in SharePoint (dnes OpenAI) se může měnit — ověřit sekci Models v get-started. Seznam ploch s Anthropicem se rozšiřuje.
