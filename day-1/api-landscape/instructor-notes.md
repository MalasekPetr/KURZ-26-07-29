# Instructor notes — Mapa API M365 & SPO + historie nástrojů

## Timing

- 60 min výklad + 15 min demo & diskuze. Blok nemá lab — je to evangelizační a
  orientační vstup, na kterém stojí zbytek týdne.

## Tón — nejdůležitější poznámka bloku

- Publikum je firma, která se M365 **bojí** používat naplno. Cíl bloku není předat fakta,
  ale změnit postoj: „API = řízené riziko s auditní stopou", ne „díra do našich dat".
  Tři argumenty (data v tenantu / vymezená oprávnění / audit) zopakovat na konci bloku
  explicitně — vracet se k nim celý týden.
- Nezabřednout do nostalgie: historie nástrojů je 15 minut, ne 45. Tabulka slouží jediné
  pointě („moduly umírají, REST zůstává") — ne výčtu verzí SharePointu.
- Žádný kód se v tomto bloku nepíše. První skript přijde v následujícím bloku
  (`formats-fundamentals`) — tady jen demo v Graph Exploreru.

## Go/no-go

- Graph Explorer funguje s kurzovním účtem `cloudedu.cz` (vyzkoušet den předem — consent
  na první přihlášení udělat předem admin consentem, ať demo nezačíná consent obrazovkou).

## Tripwires

- Otázka „a nemůže nám tím někdo smazat tenant?" přijde — odpověď: přesně proto D2 učí
  permissions, least privilege a certifikáty; nebagatelizovat, slíbit konkrétní odpověď
  a splnit ji.
- Rozdíl Graph vs SharePoint REST nedrilovat do detailu — v D1 stačí „jedna brána vs
  specialista"; rozhodovací osa patří do `day-2/automation-strategy`.

## Vazby

- Dopředu: rozhodovací osa wrapper/REST v [`../../day-2/automation-strategy/`](../../day-2/automation-strategy/);
  permissions a identita v celém D2; Graph Explorer se vrací v [`../../day-3/graph-fundamentals/`](../../day-3/graph-fundamentals/).
- Zpět: navazuje na účty a pravidla z [`../onboarding/`](../onboarding/).
