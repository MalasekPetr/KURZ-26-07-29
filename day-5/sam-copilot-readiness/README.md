# SharePoint Advanced Management (SAM) — připravenost na Copilot

> Typ: povinný · Den: 5 (úvodní náhled) · Odhad: 20 min náhled v admin centru
> Navazuje na: governance nit z [`../../day-1/onboarding/ways-of-working.md`](../../day-1/onboarding/ways-of-working.md) a permissions z D2

Rychlý náhled na nástroje, kterými se **před** nasazením Copilota zjišťuje, co je
v tenantu přesdílené. Zařazeno na začátek dne záměrně: než začneme agenty stavět,
je dobré vidět, čím se ověřuje, že nebudou vidět víc, než mají.

## Proč to patří k AI dni

Copilot a agenti čtou obsah **s oprávněními přihlášeného uživatele** (permission
trimming z D4). Z toho plyne nepříjemný důsledek: **co je přesdílené, to Copilot najde
rychleji než člověk.** SAM je sada správcovských nástrojů, která přesdílení najde
a pomůže ho zúžit — proto je „SAM report" první krok každého rozumného rollout plánu.

## Co SAM umí (skupiny nástrojů)

| Skupina | Nástroje |
|---|---|
| **Oversharing control** | Content management assessment (rozcestník), **Data access governance (DAG) reporty** — sharing links, sensitivity labels (vyžaduje E5/G5), **EEEU insights** („Everyone except external users"), permission state reporty pro weby/OneDrive/soubory · **Site access review** (delegování revize vlastníkům webů) · **Restricted access control (RAC)** — omezení webu na skupinu · **Restricted content discovery (RCD)** — web se neobjeví v Copilotu · Block download policy · Enterprise app insights · Conditional Access přes authentication contexts |
| **Sprawl control** | Site ownership policy, Inactive sites policy, **Site attestation policy** — vše v režimu **simulation** i **active** |
| **Content lifecycle** | Catalog management, Change history (web i org nastavení), Recent admin actions |
| **AI nad reporty** | **SharePoint agent insights** — tlačítko *Get AI insights* u reportů vytáhne z reportu vzory a navrhne akce |
| Porovnání politik | Compare SharePoint site policies — najde weby s podobným obsahem a jinou úrovní ochrany |

## Licencování — bez add-onu, ale ne úplně

- Přiřadí-li organizace **aspoň jednu M365 Copilot licenci**, dostanou SharePoint
  správci **SAM funkce podporující nasazení Copilota** — tj. prakticky celý výše uvedený
  výčet. Platí i pro GCC, GCC-High a DoD.
- **Výjimka**: *Restricted site creation by apps* (které aplikace smí zakládat weby)
  v Copilot licenci **není** — vyžaduje **SAM Plan 1 add-on**.
- *Advanced tenant renaming* není v GCC/GCC-H/DoD; nad 10 000 webů vyžaduje SAM.
- Report *Sensitivity labels* navíc potřebuje **E5/G5**.

## Náhled v admin centru (co si projít)

1. **SharePoint admin center → Reports → Data access governance** — jeden report
   otevřít a pojmenovat, co ukazuje (typicky „Sharing links" nebo „EEEU").
2. U reportu použít **Get AI insights** — ukázka, že AI tady pomáhá *správci*, ne
   koncovému uživateli.
3. **Site access review** — jak se revize deleguje vlastníkovi webu (governance se
   nedělá centrálně za všechny).
4. **Restricted content discovery** na jednom webu — a rovnou pointa: web s RCD
   **nenabídne Copilot in SharePoint ani AI akce**, ať je `KnowledgeAgentScope`
   nastavený jakkoli ([`../sharepoint-skills/`](../sharepoint-skills/)).
5. **Site attestation policy** v simulation mode — pravidelné potvrzování vlastníky.

## Klíčové rozlišení
- **Simulation vs active mode** — politiku lze nejdřív jen měřit (kdo by dostal výzvu),
  než začne reálně jednat. U 25 GA v jednom tenantu i u produkce je simulace první krok.
- **RAC vs RCD** — RAC omezuje **přístup** na web (kdo se dostane dovnitř), RCD omezuje
  **objevitelnost** v Copilotu a hledání (kdo web najde). Jiný nástroj na jiný problém.
- **DAG report vs audit log** — DAG je *stav* („co je dnes přesdílené"), audit log je
  *historie* („kdo co udělal"); pro Copilot readiness potřebujete stav.
- **SAM (správce) vs Purview (data)** — SAM řeší weby, oprávnění a sdílení; Purview
  labely, retenci a DLP. V rollout plánu se doplňují.

## Tipy
- SAM report udělejte **před** pilotem Copilota a znovu po měsíci — je to jediný způsob,
  jak ukázat, že se governance zlepšuje, ne zhoršuje.
- **EEEU report** je nejrychlejší nález: „Everyone except external users" na knihovně
  s HR daty je klasika, kterou Copilot spolehlivě zviditelní.
- Než něco zapnete v active mode, projděte simulaci a **mluvte s vlastníky webů** —
  automatická výzva bez kontextu vyvolá odpor, ne úklid.
- RCD berte jako **dočasné rychlé opatření** u rizikových webů, ne jako trvalé řešení
  přesdílení; trvalé řešení je zúžení oprávnění.

## Zdroje (Microsoft)
- [SharePoint Advanced Management overview](https://learn.microsoft.com/en-us/sharepoint/advanced-management)
- [SAM funkce zahrnuté v M365 Copilot licencích](https://learn.microsoft.com/en-us/sharepoint/sharepoint-advanced-management-features-copilot-license)
- [Get ready for Microsoft 365 Copilot with SAM](https://learn.microsoft.com/en-us/sharepoint/get-ready-copilot-sharepoint-advanced-management)
- [Data access governance reports](https://learn.microsoft.com/en-us/sharepoint/data-access-governance-reports)
- [Restricted content discovery](https://learn.microsoft.com/en-us/sharepoint/restricted-content-discovery)
- [Site attestation policy](https://learn.microsoft.com/en-us/sharepoint/request-site-attestations)

## Stav produktu / delta
> [!WARNING] Ověřit k datu příštího běhu
> Rozsah SAM funkcí zahrnutých v Copilot licencích se rozšiřuje (tabulka na Learn se
> mění po měsících) a názvy reportů v admin centru se přesouvají. Před během proklikat
> cestu Reports → Data access governance a ověřit, které funkce tenant reálně nabízí.
