# Tahák · Troubleshooting připojení: „jsem připojený? a kdo vlastně jsem?"

Postup pro chvíli, kdy `Connect-PnPOnline` „projde", ale nic nefunguje — nebo neprojde
vůbec. Klíčová mentální kotva: **authn ≠ authz** — *připojil jsem se* (autentizace)
ještě neznamená *smím něco* (autorizace). Každá vrstva má vlastní diagnostiku.

## Krok 1 — ověření připojení (tři úrovně důkazu)

```powershell
# a) Stav v paměti — kam a jako kdo si PowerShell MYSLÍ, že je připojený
Get-PnPConnection | Select-Object Url, ConnectionType, ClientId, Tenant

# b) Co je v tokenu — koho/co token skutečně reprezentuje
$t = Get-PnPAccessToken -ResourceTypeName SharePoint -Decoded
$t.Payload.aud     # audience: pro SPO volání musí být https://<tenant>.sharepoint.com
$t.Payload.roles   # app-only: aplikační oprávnění (Sites.FullControl.All…)
$t.Payload.upn     # delegated: přihlášený uživatel (u app-only chybí — správně!)

# c) Reálné volání — jediný skutečný důkaz
Get-PnPWeb | Select-Object Title, Url
Get-PnPTenantSite | Select-Object -First 3     # jen na -admin URL
```

(a) je jen objekt v paměti — existuje i s nefunkčním tokenem. (b) říká, co token
opravdu nese: **app-only má `roles` a nemá `upn`; delegated má `upn`+`scp` a nemá
`roles`** — nejrychlejší způsob, jak rozlišit, „kdo jsem". (c) je teprve důkaz.

## Krok 2 — tabulka symptomů

| Symptom | Příčina | Oprava |
|---|---|---|
| `AADSTS700016` (no application found) | `Connect-PnPOnline` bez `-ClientId` — PnP od 9/2024 nemá výchozí | doplnit `-ClientId` vlastní app registrace (pravidlo 9 priming promptu) |
| `AADSTS7000218` (must contain client_assertion or client_secret) | device code / flow bez redirect URI a vypnutý fallback *Allow public client flows* | app registrace → *Authentication → Settings* → přepnout na *Yes* |
| „The provided certificate is not of type **RSA**" | ECC certifikát — Entra/MSAL podporuje pro app-only jen RSA podpisy | vygenerovat RSA2048 klíč + cert, vyměnit `.cer` na app registraci (nový thumbprint!) |
| Připojení projde, ale **`Unauthorized`** s prázdnou odpovědí na první cmdlet | token bez rolí — typicky oprávnění přidané jako **Delegated** místo **Application** (app-only delegated oprávnění ignoruje), nebo chybí admin consent | *API permissions* → SharePoint → **Application** → `Sites.FullControl.All` → **Grant admin consent**; pak **nové připojení** (starý token roli nedostane) |
| Totéž — a Application permission „tam je" | consent udělen **po** připojení; token v paměti je starý | `Disconnect-PnPOnline` / nová konzole a připojit znovu |
| `403 Forbidden` (ne 401) | token role má, ale nestačí na operaci (např. čtecí role vs. zápis) | porovnat `roles` v tokenu s potřebou cmdletu; rozšíření zdůvodnit (hardening!) |
| Cert nikde ve store (`Cert:\CurrentUser\My`) po YubiKey demu | cert se propisuje při **vložení** klíče; nebo neběží služba propagace / chybí minidriver | vytáhnout/vložit klíč; `Get-Service CertPropSvc` (+ `Start-Service`); `winget install Yubico.YubiKeySmartCardMinidriver`; diagnostika `certutil -scinfo` |
| Export z YubiKey vrátil „starý" certifikát | `certificates generate` selhal (špatný PIN…) a ve slotu zůstal předchozí cert — **klíč a cert jsou v PIV dva samostatné objekty**, `keys generate` starý cert nemaže | zopakovat `certificates generate` (pozor na zbývající PIN pokusy!), pak teprve export; ověřit pár: `(Get-Item Cert:\...\<thumb>).PublicKey.Oid.FriendlyName` → RSA |
| „**Access was denied because of a security violation**" (žádný `AADSTS…`) | **lokální** krypto chyba — podpis privátním klíčem neproběhl, požadavek nikdy neodešel. Nejčastěji: klíč s `--touch-policy always` čekal na **dotyk** a vypršel (~15 s); nebo se PIN dialog nemá kde zobrazit (VS Code integrovaný terminál); nebo cert ve store není spárovaný s klíčem na kartě | dotknout se klíče, jakmile bliká dioda; spustit ve **samostatné konzoli** (`pwsh` z Windows Terminal, ne v editoru); izolovat podpis od MSAL (viz test níže); pro demo zvážit `--touch-policy never` |

### Izolace podpisu — funguje vůbec klíč?

Obejde PnP i MSAL a testuje jen čip — nejrychlejší diagnóza „security violation":

```powershell
$c = Get-Item Cert:\CurrentUser\My\<thumbprint>
$c.HasPrivateKey          # musí být True
$rsa = [System.Security.Cryptography.X509Certificates.RSACertificateExtensions]::GetRSAPrivateKey($c)
$rsa.SignData([byte[]](1..32), 'SHA256', 'Pkcs1') | Out-Null   # → PIN dialog + blikání + dotyk
```

- **Projde** → čip je v pořádku; problém je v kontextu volání (terminál) nebo v načasování dotyku.
- **Spadne stejně** → mezi Windows a klíčem: `Get-Service CertPropSvc`, minidriver,
  `certutil -scinfo`; a pokud je `HasPrivateKey` = `False`, cert ve store není spárovaný
  s klíčem na kartě (typicky po nekonzistentním stavu slotu — přegenerovat klíč i cert).

## Dvě pasti na závěr

- **Stejná slova, dvě API**: SharePoint delegated se jmenuje `AllSites.FullControl`,
  SharePoint application `Sites.FullControl.All` — a Graph má taky `Sites.FullControl.All`,
  které ale pro SPO REST volání (`Get-PnPWeb`, `Get-PnPTenantSite`) nepomůže. Vždy
  kontrolovat **API + Type + Status** (zelená fajfka), ne jen jméno.
- **Token žije v paměti připojení**: každá změna oprávnění/consentu se projeví až
  v novém tokenu — po změně v portálu vždy `Disconnect-PnPOnline` a připojit znovu;
  počítat s ~1–2 min propagací.

## Zdroje (Microsoft)

- [Microsoft identity platform — chybové kódy AADSTS](https://learn.microsoft.com/en-us/entra/identity-platform/reference-error-codes)
- [Granting access via Azure AD App-Only (PnP)](https://learn.microsoft.com/en-us/sharepoint/dev/solution-guidance/security-apponly-azuread)
- [Permissions and consent overview](https://learn.microsoft.com/en-us/entra/identity-platform/permissions-consent-overview)
