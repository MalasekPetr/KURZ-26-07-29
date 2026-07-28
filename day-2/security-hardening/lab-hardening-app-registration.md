# Lab · Hardening app registration & úprava scope

> Odhad: 60 min · Režim: živý tenant

## Cíl

Účastník provede audit permissions app registrace z [`../automation-strategy/`](../automation-strategy/) (Lab 2 + rozšíření
z Labu 3), odebere nadbytečná oprávnění a provede rotaci certifikátu bez výpadku
(overlap starý/nový v `keyCredentials`).

## Předpoklady

- App registrace z [`../automation-strategy/`](../automation-strategy/) s historií použití z dnešních labů (Lab 2 a 3).
- Certifikátové app-only přihlášení z Labu 3 funkční ([`../powershell-deep-dive/lab-cert-auth-sites.md`](../powershell-deep-dive/lab-cert-auth-sites.md)).
- Oprávnění spravovat tuto app registraci (účastník ji vlastní od Labu 2).

## Kroky

1. Vypsat všechny permissions aktuálně přiřazené app registraci.
2. Ke každému permission napsat, kde přesně (který lab/krok) bylo reálně použito.
3. Odebrat permissions, které nebyly použité, nebo mají užší dostupnou alternativu
   (typicky: stačilo by místo `Sites.FullControl.All` jen `Sites.Manage.All`? zdůvodnit).
4. Simulovat plánovanou rotaci: vygenerovat **nový** certifikát, nahrát ho vedle
   stávajícího (`keyCredentials` je multi-hodnotové pole), otestovat připojení s novým
   thumbprintem, teprve pak odebrat starý certifikát. Pokud na app registraci z dřívějška
   visí client secret, odstranit ho úplně — produkce jede jen na cert.

## Ověření

- [ ] Seznam permissions po auditu je kratší (nebo zdůvodněně stejný) oproti seznamu před
      auditem, s explicitním zdůvodněním u každé ponechané položky.
- [ ] Připojení novým certifikátem funguje před odebráním starého (ověřený overlap,
      ne "nejdřív smazat, pak zkusit").
- [ ] Na app registraci zůstává po labu právě jeden platný certifikát a žádný client secret.

## Fallback

Pokud generování/nahrání nového certifikátu selže kvůli omezením prostředí, účastník
provede jen audit a odebrání permissions (kroky 1-3) a rotaci popíše jako plán
(kroky, v jakém pořadí, jak ověřit) bez reálné exekuce.
