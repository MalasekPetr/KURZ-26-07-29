# Instructor notes — Security hardening & least privilege

## Timing

- 40 min výklad + 60 min lab.

## Go/no-go — KLÍČOVÉ, otestovat před během

- Připravit přehled permissions přiřazených app registraci během dnešních labů (pokud
  účastníci sami neevidovali) — pomůže s auditem; zdůvodnění si měli zapisovat od Labu 2.
- Ověřit, že vygenerování self-signed certifikátu a jeho nahrání na app registraci funguje
  v kurzovém prostředí bez blokace (firewall/policy).

## Tripwires

- Trvat na pořadí "nahrát nový cert → ověřit → teprve pak smazat secret", ne obráceně —
  to je přesně bod labu (zero-downtime rotace).
- Nezaměňovat Conditional Access na service principal (musí být přiřazená přímo, ne přes
  skupinu) s CA na uživatele (skupiny fungují běžně) — časté nedorozumění.

## Vazby

- Dopředu: hardening checklist se vrací v [`../../day-3/capstone-mini/`](../../day-3/capstone-mini/)
  a v rollout blueprintu D5 capstone.
- Zpět: navazuje na auth módy z [`../powershell-deep-dive/`](../powershell-deep-dive/),
  žebříček credentials z [`../certificates-and-keys/`](../certificates-and-keys/) a app
  registration strategii z [`../automation-strategy/`](../automation-strategy/) — uzavírá
  least-privilege osu dne 2.
