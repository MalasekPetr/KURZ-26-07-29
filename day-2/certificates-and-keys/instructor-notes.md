# Instructor notes — Certifikáty a klíče v praxi

## Timing

- 45 min výklad + 30 min YubiKey demo. Zařazeno hned po Labu 3 (`powershell-deep-dive`)
  — účastníci mají čerstvě vygenerovaný vlastní cert, výklad mu dává širší rámec.

## Go/no-go — KLÍČOVÉ

- Celé YubiKey demo projet na instruktorském stroji den předem (ykman verze, minidriver,
  PIN) — viz prerekvizity v [`demo-yubikey.md`](demo-yubikey.md). Mít screenshoty
  z generálky jako fallback.
- Mít s sebou minimálně 2 YubiKey (druhý pro kolečko mezi účastníky — ať si dotyk
  a PIN prompt osahají, bez přístupu k instruktorské app).

## Tripwires

- Tabulka formátů je jádro bloku — vracet se k ní: každou otázku typu „a co je .p7b /
  .crt / .key?" mapovat na sloupce „obsahuje privátní klíč?" a „smí opustit stroj?".
- Neutopit blok v kryptografii (RSA vs ECC, délky klíčů) — jedna věta „ECC P-384 je
  dnešní rozumný default" a dál. Publikum potřebuje provozní jistotu, ne teorii.
- Otázka „proč nestačí heslo/secret?" — odpověď propojit s Labem 3 (secret se dá opsat,
  cert s NonExportable ne) a s hardening blokem (preferenční pořadí credentials).
- Rozlišovat role YubiKey: D1 MFA pro člověka vs dnešní PIV credential — účastníkům
  se to plete; věta „stejný hardware, dvě různé přihrádky" funguje.

## Vazby

- Dopředu: [`../security-hardening/`](../security-hardening/) staví žebříček
  managed identity → cert → secret a rotaci; PEM formát se vrací u kontejnerů
  v [`../../day-3/azure-orientation/`](../../day-3/azure-orientation/).
- Zpět: staví na certu a app registraci z [`../powershell-deep-dive/`](../powershell-deep-dive/)
  (Lab 3) a [`../automation-strategy/`](../automation-strategy/) (Lab 2); MFA setkání
  s hardware tokenem v [`../../day-1/onboarding/mfa-setup.md`](../../day-1/onboarding/mfa-setup.md).
