# Lab · Readiness checklist a gap log

> Modul: AI landscape · Režim: simulace / řízená diskuse · Prostředí: `../../environment.md`

## Cíl
Student sestaví Copilot readiness checklist a zaznamená gapy tenantu — s důrazem na datové hranice a governance.

## Předpoklady
- Přístup do tenantu (viz onboarding).
- Pochopení grounding / permission-trimming z modulu.

## Kroky
1. Projdi readiness oblasti a u každé zaznamenej stav (OK / gap / N/A):
   - **Identita & přístup**: Entra ID, MFA, Conditional Access.
   - **Grounding foundation**: kvalita permissions, oversharing, sensitivity labels.
   - **Datové hranice**: EU Data Boundary očekávání; vědomí vyňetí web queries a Anthropic modelů.
   - **Licencování**: kdo má Copilot / PAYG (viz `../licensing/`).
   - **Governance**: pravidla práce, subprocessor rozhodnutí.
2. Pro každý **gap** zapiš dopad a navrhovanou akci do gap logu.

## Gap log (šablona)
| Oblast | Stav | Dopad | Akce | Vlastník |
|---|---|---|---|---|
| | | | | |

## Ověření
- [ ] Checklist pokrývá 5 oblastí výše.
- [ ] Každý gap má dopad + akci.
- [ ] Zaznamenán aspoň jeden bod k datovým hranicím (EU / Anthropic vyňetí).

## Fallback
Když není přístup do tenantu, veď jako řízenou diskusi nad modelovým tenantem a checklist vyplňte společně.
