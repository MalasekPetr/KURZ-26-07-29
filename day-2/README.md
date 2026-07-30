# Den 2 — Klíče od API: identita, permissions a certifikáty

Kdo automatizace je (app registrace, delegated vs application permissions), čím se
prokazuje (PowerShell moduly a autentizační módy, certifikát v Labu 3), jak credential
bezpečně uložit (formáty CER/PEM/PFX, cert stores, YubiKey živé demo) a jak to celé
udržet čisté (least privilege audit a rotace bez výpadku).

| Pořadí | Blok | Slug | Typ |
|---|---|---|---|
| 1 | Nástrojová mapa & app registrace *(Lab 2)* | [`automation-strategy`](automation-strategy/) | P |
| 2 | PowerShell moduly & autentizace *(Lab 3 + mini-lab: tři podpisy zápisu)* | [`powershell-deep-dive`](powershell-deep-dive/) | P |
| 3 | Certifikáty a klíče: CER/PEM/PFX, stores, YubiKey *(demo)* | [`certificates-and-keys`](certificates-and-keys/) | P |
| 4 | Security hardening & least privilege | [`security-hardening`](security-hardening/) | P |

> [!NOTE]
> Nejhustší den kurzu — odpovídá na otázku z D1 „a nemůže nám tím někdo smazat tenant?".
> Lab 3 vytváří pracovní weby `-dev/-test/-prod` a `Connect-CourseTarget` wrapper,
> na kterých staví oba laby D3. Hardening na závěr audituje přesně to, co během dne
> vzniklo — oprávnění se dají kdykoli zpětně zúžit a doložit auditem.
