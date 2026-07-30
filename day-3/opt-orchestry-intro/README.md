# Orchestry — úvod do 3rd-party governance platformy (volitelné)

> Typ: **volitelný** · Den: 3 · Odhad: 20 min výklad (bez labu)

Orientační blok o produktu třetí strany. **V kurzu není licence**, takže žádné hands-on
ani živé demo — cílem je vědět, jaký problém takové platformy řeší, čím se liší od
nativních nástrojů z D3, a podle čeho se rozhodnout „koupit, nebo postavit".

## Cíle
- Umět pojmenovat problém, který governance platformy adresují: **samoobslužné
  zřizování workspaců s pravidly** (schvalování, naming, lifecycle, archivace).
- Porovnat Orchestry s nativní cestou, kterou jsme se učili (site templates, PnP
  šablony, skripty, Purview).
- Znát rozhodovací kritéria buy vs. build — a rizika obojího.

## Výklad

### Jaký problém řeší
Nativní SharePoint umí web vytvořit. Neumí sám od sebe:

- **žádanku a schvalovací proces** („chci web pro projekt X" → schválí vedoucí → vznikne
  web podle šablony s předvyplněnými metadaty),
- **vynucené pojmenování a metadata** napříč Teams i SharePointem,
- **lifecycle**: kontrola aktivity, výzvy vlastníkům, archivace a smazání nepoužívaných
  workspaců,
- **jednotný katalog šablon** pro Teams, SharePoint i Vivu s vlastním brandingem.

Tohle všechno lze postavit vlastními silami (žádanka v seznamu + Power Automate +
provisioning skript + reporty) — a přesně to dělá modul
[`backlog/provisioning-patterns`](../../backlog/provisioning-patterns/). Platformy typu
Orchestry, AvePoint Cloud Governance nebo ShareGate to nabízejí jako hotový produkt.

### Nativně vs. platforma
| Potřeba | Nativně (D3) | Platforma |
|---|---|---|
| Struktura nového webu | site template / PnP šablona | katalog šablon s brandingem |
| Žádanka + schválení | vlastní seznam + Power Automate | hotový workflow |
| Naming & metadata | konvence + kontrolní skript | vynuceno při zřízení |
| Lifecycle / archivace | vlastní skripty + Purview | vestavěné, s reportingem |
| Cena | čas vašich lidí | licence per uživatel/rok |
| Závislost | na vašem kódu (a autorovi) | na dodavateli |

### Buy vs. build — kritéria
Postavit vlastní se vyplatí, když: máte kapacitu skriptovat (po tomto kurzu ji máte),
požadavky jsou jednoduché a stabilní, a chcete plnou kontrolu nad tím, kde data
a logika žijí. Koupit dává smysl, když: workspaců je hodně a vznikají neustále,
governance má vlastníka v byznysu (ne v IT), a náklad na údržbu vlastního řešení
přeroste licenci.

Pro organizace veřejné správy dva doplňkové body: **kde platforma zpracovává data**
(vlastní tenant vs. služba dodavatele) je otázka do posouzení, a **exit strategie** —
co zůstane, když licenci nezaplatíte (weby ano, workflow a katalog ne).

## Klíčové rozlišení
- **Provisioning (vytvořit) vs governance (řídit po celou dobu života)** — nativní
  nástroje z D3 řeší první, platformy oba.
- **Vlastní řešení vs produkt** — nejde o „lepší/horší", ale o to, kdo nese náklad
  údržby a kde je znalost.
- **Vendor demo vs vlastní test** — platformy vypadají v demu skvěle; než se rozhodne,
  chce to pilot na reálných požadavcích vaší organizace.

## Zdroje
- [Orchestry — dokumentace dodavatele](https://docs.orchestry.com/)
- [Microsoft — Governance in Microsoft 365 groups a SharePoint](https://learn.microsoft.com/en-us/microsoft-365/solutions/collaboration-governance-overview)
- Nativní cesta v tomto repu: [`backlog/provisioning-patterns`](../../backlog/provisioning-patterns/),
  [`../site-list-templates/`](../site-list-templates/)

## Stav produktu / delta
> [!WARNING]
> Ověřit k datu běhu — funkce, licencování a pozicování 3rd-party platforem se mění
> rychle a nezávisle na Microsoftu; tento blok drží úroveň „jaký problém to řeší",
> ne feature list. Materiál není dodavatelem sponzorovaný ani schvalovaný.
