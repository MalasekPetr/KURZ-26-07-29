# KURZ-26-07-29 — M365 API & skriptování od základů + AI/Copilot (zakázkový kurz, od 29. 7. 2026)

Zakázkový 5denní běh sestavený z materiálů kurzů GOPAS **GOC223** (Microsoft 365:
Pokročilá automatizace a migrace SharePoint) a **GOC224** (Microsoft 365: správa
SharePoint Copilot a obsahových služeb), doplněný o nové úvodní moduly.

**Cíl dnů 1–3 (revize 2026-07-28):** sebejistě používat API SharePointu a M365 od
úplných základů — rozumět tomu, co se pod nástroji děje, skriptovat s jistotou,
rozlišovat mezi nástroji a používat **M365 Copilot Chat (free)** pro přípravu
a testování skriptů. Žádné vstupní znalosti se nepředpokládají.

| Část | Dny | Zdroj | Obsah |
|---|---|---|---|
| **API & skriptování** | 1–3 | GOC223 (výběr) + nové moduly | mapa API SPO/M365 a vývoj nástrojů, formáty dat (JSON/YAML/XML/CSV, UTF-8) a PowerShell od nuly, app registrace a API permissions (delegated vs application), certifikáty (CER/PEM/PFX, stores, YubiKey), Graph prakticky, Azure orientace, kontejnery, Node/TypeScript |
| **AI & Copilot** | 4–5 | GOC224 | AI landscape, licencování, prompting, tvorba agentů (Agent Builder, SharePoint agents, Agents Toolkit, Copilot Studio), správa Copilotu, rollout |

## Jak repo číst

- **Pořadí modulů** je definované v [`agenda.md`](agenda.md) — složky jsou pojmenované
  **slugy**, ne čísly. Jediný zdroj pravdy o pořadí je `agenda.md`.
- **Závazné názvosloví** je v [`GLOSSARY.md`](GLOSSARY.md) (sloučený glosář obou kurzů).
- **Prostředí kurzu** (tenanty, Azure, PAYG) je v [`environment.md`](environment.md).
- Moduly s prefixem `opt-` jsou **volitelné** — spouští se dle času; nikdy na nich
  nesmí záviset povinný modul ani capstone.
- **`backlog/`** obsahuje moduly mimo agendu tohoto běhu — vyřazené revizí 2026-07-28
  (viz níže) i nově napsané kandidáty (např. `git-fundamentals`) pro navazující běh.
- Currency-markery v textu (interní konvence; marker + popis na prvním řádku bloku):
  - `> [!WARNING]` + „Ověřit k datu běhu" — fast-moving fakt (ceny, preview, throttling limity, verze modulů).
  - `> [!IMPORTANT]` — lineage / přejmenování / API breaking change, na které účastníky upozornit.

## Co bylo vypuštěno (a proč)

**Revize 2026-07-28** (přeorientování dnů 1–3 na základy API a skriptování): z původní automatizační části
přesunuty do [`backlog/`](backlog/) — kandidáti na navazující běh pro pokročilé:

| Vypuštěno (→ backlog/) | Původně | Důvod |
|---|---|---|
| Staging DEV/TEST/PROD (diff/baseline) | D2 | inženýrská hloubka mimo cíl tohoto běhu |
| Skladba migrací (SPMT, wave planning) | D2 | migrace nejsou v zadání tohoto běhu |
| Vzory automatizace zřizování (PnP šablony) | D3 | staví na stagingu; pokročilé téma |
| Azure integrační vzory (Functions, change notifications) | D3 | nahrazeno orientačním `day-3/azure-orientation` |
| Lifecycle & compliance enforcement | D3 | governance hloubka mimo fokus |
| SIEM přes Azure Blob | D3 (opt) | pokročilé, závislé na Azure labu |

Původní výběr z plných kurzů (GOC223/GOC224, komprese 2×5 dní → 3+2 dny) navíc
vypustil — zůstává v mateřských repech:

| Vypuštěno | Zdroj | Důvod |
|---|---|---|
| Architektonický přehled (opt) | GOC223 D1 | volitelný; klíčové pojmy pokryje `automation-strategy` a glosář |
| Orchestry (obě varianty) | GOC223 D3 / GOC224 D5 | 3rd-party simulace bez licence, volitelné leaf nody |
| Microsoft Clarity | GOC223 D4 | okrajové pro cílovou skupinu |
| SPFx základy & App Catalog | GOC223 D5 | vývojářské téma; zmínka v `day-3/node-typescript` |
| Formáty, SharePoint úvod, IA | GOC224 D1 | ~~audience nepotřebuje úvod~~ **revize 2026-07-28: úvod do formátů obnoven jako nový modul `day-1/formats-fundamentals`** |
| SharePoint PowerShell (SPO) | GOC224 D2 | kryje `day-2/powershell-deep-dive` |
| Konfigurace, eSignature, SAM, Backup, Archive | GOC224 D2–4 | obsahové služby mimo AI fokus zadání |
| Power Automate — faktury, pro-code vs. low-code | GOC224 D3 | rozhodovací osu pokryje `copilot-agents` srovnání |
| Skills (Copilot in SharePoint) | GOC224 D5 | preview, licenčně vratké; zmínka v mapě cest agentů |
| Document processing | GOC224 D2 | ponecháno jako **volitelný** blok D4 |
| Monitoring & compliance | GOC224 D5 | ponecháno jako **volitelný** blok D5 |

## Struktura

```text
KURZ-26-07-29/
├─ README.md            # tento soubor
├─ GLOSSARY.md          # závazné názvosloví (sloučené z GOC223 + GOC224)
├─ agenda.md            # 5denní pořadí bloků (single source of order)
├─ environment.md       # pracovní tenanty a Azure
├─ scripts/             # provozní skripty kurzu — zatím scaffold (plán v scripts/README.md)
├─ backlog/             # moduly vyřazené revizí 2026-07-28 (kandidáti na navazující běh)
├─ day-1/ … day-3/      # API & skriptování od základů (výběr z GOC223 + nové moduly)
├─ day-4/ … day-5/      # AI & Copilot (moduly z GOC224)
```

## Licence

Všechna práva vyhrazena (Malach IS s.r.o.) s výjimkou pro účastníky kurzu — osobní
studium a užití skriptů ve vlastní organizaci. Detail: [`LICENSE.md`](LICENSE.md).

## Provenience

Převzaté moduly jsou kopie z GOC223/GOC224 k 2026-07-18; moduly `day-1/api-landscape`,
`day-1/formats-fundamentals`, `day-2/certificates-and-keys`, `day-3/azure-orientation`,
`day-3/node-typescript` a `day-3/capstone-mini` vznikly pro tento běh (revize 2026-07-28).
Odkazy uvnitř modulů na bloky, které v tomto výběru nejsou, vedou do `backlog/` nebo
textově na plný kurz — nejsou to chybějící soubory, ale záměrně vypuštěný obsah.
Opravy převzatého obsahu dělat v mateřských repech a sem přenášet; nové moduly se
opravují přímo zde.
