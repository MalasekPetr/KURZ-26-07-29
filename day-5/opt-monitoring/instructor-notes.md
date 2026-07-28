# Instructor notes — Monitoring

## Timing

- 40 min výklad + 35 min lab. Audit hands-on je „wow" moment (studenti vidí vlastní stopu) — nechat mu prostor.

## Go/no-go

- **Ověřit, jak studenti dostanou audit search**: Global Reader na Purview Audit nestačí vždy — otestovat; případně dočasně View-Only Audit Logs role, jinak fallback (promítání).
- Audit eventy mají zpoždění — interakce z D4 budou vidět, ranní z D5 nemusí. Neplánovat lab na „co jsi dělal před hodinou".

## Tripwires

- Alert policies ukazovat v **Defender portálu** — kdo je zná z Purview compliance portálu, hledá je špatně (přestěhováno).
- `JailbreakDetected` a `BingWebSearch` v záznamu — zmínit, výborný teaching point (co všechno stopa nese), ale nedemonstrovat jailbreak!
- Usage report ≠ audit — MS to říká explicitně, opřít se o to.
- Runbook je deliverable dle osnovy — nenechat sklouznout jen k prohlížení auditu.

## Vazby

- Zpět: signály (PAYG spotřeba D4–D5, agent dashboard D5 `copilot-admin`) se potkávají v runbooku; DAG reporty, backup drilly a eSignature audit jsou mimo tento běh (plný kurz GOC224).
- Dopředu: runbook = sekce capstone blueprintu (D5).
