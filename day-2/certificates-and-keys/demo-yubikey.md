# Demo · Privátní klíč na YubiKey — app-only auth s dotykem

> Odhad: 30 min · Režim: živé demo (předvádí lektor, klíč koluje k osahání)

## Co demo ukazuje

Celý životní cyklus hardwarového credentialu: klíč vzniká **uvnitř** YubiKey, ven jde
jen veřejná část, a přihlášení aplikace proti SharePoint Online proběhne s PIN +
fyzickým dotykem. Pointa: **privátní klíč nikdo neukradne, protože fyzicky neexistuje
mimo čip.**

## Průběh — co sledovat

1. **Co je v ruce**: `ykman list`, `ykman piv info` — sloty a PIN policy. Tentýž typ
   klíče, který v D1 sloužil jako MFA token člověka, umí držet i PIV certifikát
   aplikace („stejný hardware, dvě různé přihrádky"). První správný krok: výchozí
   PIV PIN `123456` je změněný — default PIN je první věc, kterou útočník zkusí.
2. **Klíč vzniká v čipu**:

   ```text
   ykman piv keys generate --algorithm RSA2048 --pin-policy once --touch-policy always 9a pubkey.pem
   ```

   Pozor: **musí být RSA** — Entra/MSAL podporuje pro certifikátové app-only přihlášení
   jen RSA podpisy; s ECC klíčem (`ECCP256/384`) skončí `Connect-PnPOnline` chybou
   *„The provided certificate is not of type RSA"*. Generování RSA v čipu trvá
   i desítky sekund — klíč opravdu počítá, nechte doběhnout.
   `pubkey.pem` otevřený v editoru je textový PEM s `BEGIN PUBLIC KEY` — **tohle je
   všechno, co kdy čip opustí** (vazba na tabulku formátů ve [`README.md`](README.md)).
3. **Certifikát nad klíčem**:

   ```text
   ykman piv certificates generate --subject "CN=kurz-yubikey-demo" 9a pubkey.pem
   ykman piv certificates export 9a demo-yubikey.cer
   ```

4. **Upload do Entra**: portál → App registrations → Certificates & secrets → upload
   `demo-yubikey.cer`. Portál ukáže jen thumbprint a platnost — žádné tajemství se
   nikam nenahrává.
5. **Připojení s dotykem**: po vložení klíče minidriver propíše certifikát do
   `Cert:\CurrentUser\My` (ověření: `Get-ChildItem Cert:\CurrentUser\My`), a pak:

   ```powershell
   Connect-PnPOnline -Url $adminUrl -ClientId $clientId -Tenant $tenant -Thumbprint $thumbprint
   Get-PnPTenantSite | Select-Object -First 3
   ```

   Windows si vyžádá PIN a klíč **dotyk** — podpis proběhl v čipu. Je to tentýž cmdlet
   jako ve vašem Labu 3; jediný rozdíl je, kde bydlí privátní klíč.
6. **Pokus o krádež** (pointa): export z `certmgr.msc` i `certutil -exportPFX` selže;
   `ykman` operaci „export privátního klíče" vůbec nemá. Srovnejte s `NonExportable`
   softwarovým certem z Labu 3 — pojistka OS vs fyzikální vlastnost čipu.

## Tipy k zapamatování

- `--touch-policy always` = dotyk při **každém** podpisu. Pro credential člověka
  (admin, konzultant) žádoucí; pro bezobslužnou automatizaci by to byla chyba nasazení
  — hardware klíč patří k člověku, scheduled task řeší LocalMachine store nebo managed
  identity (viz žebříček v [`README.md`](README.md)).
- YubiKey má víc funkcí vedle sebe: PIV (čipová karta — dnešní demo), FIDO2/WebAuthn
  (passwordless přihlášení), OTP. Jsou to oddělené „přihrádky" téhož hardwaru.
- První připojení po vložení klíče může trvat déle (propagace certifikátu do store) —
  nechat doběhnout.

## Q&A · Co dělat při ztrátě klíče?

Častá (a správná) otázka: „Když ztratím jeden YubiKey z páru, přihlásím se druhým
a všechna povolení odvolám?" Skoro — ale s dvěma opravami:

- **Druhý klíč není kopie prvního.** Privátní klíč z čipu nejde dostat (pointa kroku 6),
  takže každý YubiKey má vlastní pár klíčů a vlastní certifikát. „Záložní pár" funguje
  jen, když ho tak předem nastavíte: na obou klíčích vygenerujete vlastní cert a **oba
  nahrajete na app registraci** — `keyCredentials` je multi-hodnotové pole (stejný
  mechanismus jako rotace bez výpadku v [`../security-hardening/`](../security-hardening/)).
- **Odvolává se credential, ne permissions.** Při ztrátě smažete z app registrace
  (Certificates & secrets) **certifikát ztraceného klíče** — podle thumbprintu — a
  pracujete dál druhým. Oprávnění (`Sites.Read.All`…) nechte být: říkají, *co aplikace
  smí*, a platí pro ni jako celek; jejich odvoláním byste rozbili i svůj zbývající klíč.
  Certifikát říká, *kdo se smí za aplikaci prohlásit* — a ztratil se právě jen jeden
  ze dvou „průkazů".

Dvě doplnění: nálezce s klíčem samotným moc nesvede (potřebuje PIN — 3 pokusy, pak
blokace — a vědět, k jakému ClientId patří), cert ale smažte hned, je to úkon na
30 sekund. A už vydaný access token dožívá ~1 hodinu i po smazání certu — okamžité
odříznutí umí až CAE (viz hardening blok). Stejný princip platí pro YubiKey jako MFA
člověka: tam ztracený kus odeberete v Entra jako přihlašovací metodu účtu.

## Vyzkoušejte si sami (volitelné)

S vlastním YubiKey 5 si celý postup zopakujete 1:1 — instalace `ykman` + minidriveru,
změna výchozích PIN kódů a bezpečnostní upozornění jsou v návodu
[`setup-ykman.md`](setup-ykman.md). Bez hardware klíče si stejný princip osaháte
s NonExportable certem z Labu 3.

## Zdroje (Yubico / Microsoft)

- [YubiKey Manager CLI (ykman) — PIV commands](https://developers.yubico.com/yubikey-manager/)
- [Yubico PIV — certificates & slots](https://developers.yubico.com/PIV/)
- [Certificate credentials for application authentication (Entra)](https://learn.microsoft.com/en-us/entra/identity-platform/certificate-credentials)
- [Smart Card Minidriver (Yubico)](https://www.yubico.com/support/download/smart-card-drivers-tools/)
