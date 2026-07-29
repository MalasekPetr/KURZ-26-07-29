# Návod · Dummy data z Copilot Chat (free)

Pravidlo kurzu zní: **do tenantu ani do promptů žádná reálná firemní/osobní data**
([`../onboarding/ways-of-working.md`](../onboarding/ways-of-working.md)). Testovací
obsah ale potřebujete pořád — seznamy webů, položky, uživatele, faktury. Copilot Chat
je na fiktivní data ideální generátor: rychlý, česky, a formát si řeknete.

## Recept na prompt

Čtyři věci, které v promptu vždy jsou — **formát, schéma, počet, čeština**:

```text
Vygeneruj JSON pole 15 fiktivních SharePoint webů. Každý objekt má pole:
name (string), owner (string, smyšlené české jméno s diakritikou),
docCount (int 0–5000), lastActivity (datum ISO 8601 v posledních 2 letech).
Jména musí být smyšlená — žádné reálné osoby ani firmy. Alespoň 3 weby ať
mají nulovou aktivitu přes 180 dní a 2 weby diakritiku i v názvu webu.
Výstup jen čistý JSON bez komentářů.
```

Varianty téhož receptu:

- **CSV** — „…výstup jako CSV se středníkem jako oddělovačem, první řádek hlavička"
  (pozor: uložit jako UTF-8, viz [`README.md`](README.md) sekce UTF-8).
- **Položky SPO seznamu** — schéma opište z reálného seznamu (interní názvy polí
  a choice hodnoty zjistíte tahákem
  [`../../day-3/graph-fundamentals/tips-spo-api.md`](../../day-3/graph-fundamentals/tips-spo-api.md)),
  ale v promptu použijte jen strukturu, žádná reálná data.
- **Dokumenty** — „vygeneruj 5 odstavců fiktivní interní směrnice o půjčování
  služebních vozidel" — obsah k nahrání do knihovny pro testy vyhledávání/agentů.

## Na co nezapomenout

- **Řekněte si o okrajové případy** — prázdné hodnoty, extrémně dlouhý název, znak
  `&` v textu, datum na přelomu roku. Dummy data, na kterých nic nespadne, nic
  netestují.
- **Diakritiku explicitně vyžádejte** („smyšlená česká jména s diakritikou") — jinak
  Copilot rád generuje ASCII a UTF-8 round-trip nemáte čím ověřit.
- **Zkontrolujte, že data jsou opravdu smyšlená** — projděte očima, jestli se
  negenerovalo reálné jméno/firma; AI výstup se ověřuje vždy, i tenhle.
- **Objem řešte opakováním, ne jedním promptem** — free verze má limit délky
  odpovědi; 200 položek = 4× „pokračuj dalšími 50, naváž na předchozí".
- Pro **hromadné nahrání** vygenerovaných dat do tenantu použijte skript (Lab 3+
  vzory) — a jsme zpět u workflow kurzu: prompt → číst → testovat → spustit.

## Kde se to v kurzu hodí

Lab 1 (`sites.json` — vlastní rozšíření), testovací obsah pracovních webů (D2–3),
vstupní JSON pro capstone úlohu 3 (D3), obsah pro grounding agentů (D4–5).
