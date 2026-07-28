# Demo (instruktor) · Privátní klíč na YubiKey — app-only auth s dotykem

> Odhad: 30 min · Režim: živé demo na instruktorském stroji, promítané

## Cíl

Účastníci vidí celý životní cyklus hardwarového credentialu: klíč vzniká **uvnitř**
YubiKey, ven jde jen veřejná část, a přihlášení aplikace proti SPO proběhne s PIN +
fyzickým dotykem. Pointa: „privátní klíč nikdo neukradne, protože fyzicky neexistuje
mimo čip".

## Prerekvizity (připravit den předem)

- YubiKey 5 (PIV), YubiKey Manager CLI (`ykman`) a YubiKey Smart Card Minidriver na
  instruktorském stroji.
- App registrace z Labu 3 (vlastní instruktorská, ne studentská) s `Sites.Read.All`
  application permission a admin consentem.
- Změněné výchozí PIV PIN/PUK/management key (ukázat jako správnou praxi, default PIN
  `123456` zmínit jako první věc, kterou útočník zkusí).

## Scénář

1. **Co držím v ruce** (2 min): `ykman list`, `ykman piv info` — sloty, PIN policy.
   Krátce: tentýž klíč, který D1 sloužil jako MFA token, umí držet PIV certifikát.
2. **Klíč vzniká v čipu** (5 min):

   ```text
   ykman piv keys generate --algorithm ECCP384 --pin-policy once --touch-policy always 9a pubkey.pem
   ```

   Otevřít `pubkey.pem` v editoru — textový PEM, `BEGIN PUBLIC KEY`: tohle je všechno,
   co kdy čip opustí. Vazba na výklad formátů.
3. **Certifikát nad klíčem** (5 min):

   ```text
   ykman piv certificates generate --subject "CN=kurz-yubikey-demo" 9a pubkey.pem
   ykman piv certificates export 9a demo-yubikey.cer
   ```

4. **Upload do Entra** (3 min): portál → App registrations → instruktorská app →
   Certificates & secrets → upload `demo-yubikey.cer`. Ukázat, že portál zobrazí jen
   thumbprint a platnost — žádné tajemství.
5. **Připojení s dotykem** (10 min): klíč vyjmout a znovu vložit (minidriver propíše
   certifikát do `Cert:\CurrentUser\My`), ověřit
   `Get-ChildItem Cert:\CurrentUser\My` → thumbprint. Pak:

   ```powershell
   Connect-PnPOnline -Url $adminUrl -ClientId $clientId -Tenant $tenant -Thumbprint $thumbprint
   Get-PnPTenantSite | Select-Object -First 3
   ```

   Windows si vyžádá PIN a klíč **dotyk** — podpis proběhl v čipu. Stejný cmdlet jako
   v Labu 3, jediný rozdíl je, kde bydlí privátní klíč.
6. **Pokus o krádež** (5 min, pointa dema): `certutil -exportPFX` / export z `certmgr.msc`
   → selže; `ykman piv keys export` neexistuje jako operace pro privátní klíč. Srovnat
   s `NonExportable` softwarovým certem z Labu 3 (pojistka OS vs fyzikální vlastnost).

## Tripwires

- MSAL + smart card KSP: první `Connect-PnPOnline` po vložení klíče může trvat déle
  (propagace certifikátu) — nechat doběhnout, nevytahovat klíč.
- `--touch-policy always` znamená dotyk při **každém** podpisu — pro demo ideální
  (viditelný moment), pro bezobslužnou automatizaci by to byla chyba nasazení; říct
  explicitně: hardware klíč patří k credentialu **člověka**, ne scheduled tasku.
- Nemluvit o FIDO2/WebAuthn větvi YubiKey víc než větou — PIV a FIDO2 jsou dvě různé
  funkce téhož hardwaru; demo je čistě PIV.

## Fallback

Pokud minidriver certifikát nepropíše nebo MSAL podpis selže, mít screenshoty kroků
5–6 z generálky a dokončit demo nad nimi; kroky 1–4 (generování v čipu, PEM, upload)
fungují vždy a nesou hlavní pointu.
