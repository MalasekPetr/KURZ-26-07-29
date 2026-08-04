# SharePoint Skills — workflow jako znovupoužitelný asset

> Typ: povinný · Den: 5 · Odhad: 30 min výklad + 20 min hands-on · Publikum: **správci i vlastníci webů**
> Kontext: mapa cest agentů → [`../../day-4/copilot-agents/`](../../day-4/copilot-agents/README.md)

Navazuje přímo na SharePoint agents: agent odpovídá na otázky nad obsahem webu, **skill
vykoná opakovatelný postup**. Nejlevnější způsob, jak dostat firemní pravidlo („takhle
u nás kontrolujeme smlouvu") do Copilotu — bez vývojáře a bez manifestu.

## Co to je

**Skill** je opakovatelný, víckrokový postup uložený jako **znovupoužitelný asset na
webu**. Zachytí organizační pravidla (standardy dokumentů, revizní checklisty), takže
Copilot dělá věc pokaždé stejně — na rozdíl od jednorázového promptu.

| Vlastnost | Fakt |
|---|---|
| Kde je uložený | soubor **Markdown** v knihovně **Agent Assets**: `/Agent Assets/Skills/<nazev-skillu>/SKILL.md` (knihovnu Copilot vytvoří, pokud chybí) |
| Jak vzniká | konverzací v chatu přirozeným jazykem → náhled draftu → uložení; existují i vestavěné skilly, které pomáhají skilly vytvářet |
| Kdo smí vytvořit | uživatel s oprávněním **Edit** na webu |
| Kdo smí použít | uživatel s oprávněním **View** na webu |
| Jak se najde | `/Skills` v chatu vypíše skilly dostupné v daném kontextu |
| Jak se spustí | Copilot relevantní skill načte sám, nebo ho vyvoláte jménem |
| Co neumí | **připojit se k externím systémům** ani **spustit vlastní kód**; smí jen to, na co má **uživatel** práva — nerozšiřuje přístup |

Pointa pro governance: skill **nepřidává oprávnění**. Je to zapsaný postup, ne nová
identita — na rozdíl od app registrace z D2 nebo agenta s akcemi z Toolkitu.

## Skill vs agent vs prompt

| | Prompt | Skill | Agent (SharePoint/Builder) |
|---|---|---|---|
| Životnost | jednorázový | **uložený a znovupoužitelný** | trvalá konfigurace |
| Co popisuje | jednu otázku | **postup, jak něco udělat** | co agent zná a jak se chová |
| Kde bydlí | nikde | `SKILL.md` na webu | manifest / konfigurace agenta |
| Kdo spravuje | uživatel | vlastník webu (Edit) | vlastník webu / správce |

Rozhodovací pravidlo: *opakuje se ten samý postup?* → skill. *Potřebuji odpovědi nad
obsahem?* → agent. *Ptám se jednou?* → prompt.

## Prostředí a licence

- **Copilot in SharePoint** (dřív Knowledge Agent / AI in SharePoint) je od
  **mid-June 2026 opt-out preview** — zapíná se automaticky uživatelům s **M365 Copilot
  licencí**, bez akce správce. Skills jsou jeho součástí.
- Řízení dostupnosti přes `Set-SPOTenant -KnowledgeAgentScope`
  (`AllSites` / `IncludeSelectedSites` / `ExcludeSelectedSites` / `NoSites` — **default
  `NoSites`**), seznam webů `-KnowledgeAgentSelectedSitesList` (max 100 URL, operace
  `Overwrite`/`Append`/`Remove`). Vyžaduje aktuální SPO Management Shell.
- **Restricted Content Discovery** (RCD) má přednost: na webu s RCD se Copilot
  in SharePoint ani AI akce neobjeví — bez ohledu na `KnowledgeAgentScope`
  (vazba na SAM, [`../sam-copilot-readiness/`](../sam-copilot-readiness/)).
- **Site AI settings** dávají vlastníkovi webu volbu, který agent se otevře z ikony
  a jestli se tlačítko Copilot skryje návštěvníkům.
- V preview platí **denní a týdenní limity použití na uživatele** (individuální,
  nesdílené); po vyčerpání se funkce dočasně vypnou.
- Model: **Microsoft-managed reasoning model od OpenAI**, nekonfiguruje se — proto
  Skills nezávisí na přepínači pro modely jiných dodavatelů
  ([`../copilot-admin/explainer-ai-subprocessors.md`](../copilot-admin/explainer-ai-subprocessors.md)).
- **Nepodporováno**: GCC, GCC High, DoD, air-gapped, 21Vianet.

## Hands-on

Viz [`lab-skill.md`](lab-skill.md) — vytvořit skill „kontrola smlouvy před podpisem",
najít ho v `/Skills`, spustit a prohlédnout si výsledný `SKILL.md` v knihovně
Agent Assets.

## Klíčové rozlišení
- **Skill vs agent** — postup vs znalost; skill se dá vyvolat i uvnitř konverzace s agentem.
- **`SKILL.md` je obyčejný soubor** — verzuje se, kopíruje mezi weby, čte se očima.
  „Konfigurace jako obsah" — ekvivalent repo-as-code návyků z D1–3 pro netechnické vlastníky.
- **Edit = tvořit, View = používat** — governance skillů je governance oprávnění webu,
  nic navíc.
- **Skill nerozšiřuje přístup** — nemůže volat externí systémy ani spouštět kód.

## Tipy
- Skilly, které mají zůstat stabilní, **vyexportujte** (`SKILL.md`) a uložte do repa —
  jsou to firemní pravidla, ne jednorázová konfigurace.
- Ve víceletém provozu hlídejte, kdo má na webu Edit: **kdo smí editovat web, smí měnit
  postupy**, které pak Copilot aplikuje na všechny.
- `/Skills` používejte jako vstupní bod při zaškolování — je to seznam „co u nás Copilot
  umí".
- Skill si nechte napsat Copilotem samotným (vestavěné skilly na tvorbu skillů), pak ho
  přečtěte a upravte — stejný workflow „návrh → čtení → test" jako u skriptů v D1.

## Zdroje (Microsoft)
- [Extend Copilot in SharePoint with skills](https://learn.microsoft.com/en-us/sharepoint/copilot-in-sharepoint-skills)
- [Get started with Copilot in SharePoint (preview)](https://learn.microsoft.com/en-us/sharepoint/copilot-in-sharepoint-get-started)
- [Copilot in SharePoint — přehled pro uživatele](https://support.microsoft.com/en-us/sharepoint/copilot-in-sharepoint/copilot-in-sharepoint-an-overview)

## Stav produktu / delta
> [!IMPORTANT] Přejmenování
> „Knowledge Agent" → „AI in SharePoint" → **„Copilot in SharePoint"**. PowerShell
> parametry si ale ponechaly preview názvy (`KnowledgeAgentScope`) — nezaměňovat
> s Copilot **agenty**.

> [!WARNING] Ověřit k datu příštího běhu
> Copilot in SharePoint je **preview** — limity použití, způsob zapnutí (u GA se
> enablement mění) i sada vestavěných skillů se hýbou. Na tomto běhu skilly fungovaly
> i studentům na PAYG bez Copilot licence, ačkoli dokumentace uvádí licenci — před
> dalším během znovu ověřit.
