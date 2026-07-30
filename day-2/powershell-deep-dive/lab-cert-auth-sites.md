# Lab 3 · Certifikát, app-only přihlášení & pracovní weby

> Odhad: 90 min · Režim: živý tenant

## Cíl

Nosný lab dne 2 (Lab 3). Účastník vezme app registraci z Labu 2
([`../automation-strategy/`](../automation-strategy/)), vybaví ji certifikátem (bezpečně
vygenerovaným a uloženým), přihlásí se app-only bez jakéhokoli promptu a skriptem si
vytvoří pracovní weby (DEV/TEST/PROD), nad kterými poběží Graph lab i mini-capstone v D3.

## Předpoklady

- App registrace `<jmeno.prijmeni>-course-app` z labu [`../automation-strategy/lab-app-registration.md`](../automation-strategy/lab-app-registration.md).
- PnP.PowerShell nainstalovaný s pinovanou verzí (viz [`explainer-module-management.md`](explainer-module-management.md)).
- Naming konvence z [`../../day-1/onboarding/ways-of-working.md`](../../day-1/onboarding/ways-of-working.md).

## Kroky

1. **Rozšířit oprávnění aplikace pro app-only**: přidat application permission
   `Sites.FullControl.All` (SharePoint) + admin consent. Zapsat si do poznámky zdůvodnění —
   přesně tohle rozšíření bude předmětem auditu v [`../security-hardening/`](../security-hardening/).
2. **Vygenerovat self-signed certifikát** do uživatelského úložiště — private key nikdy
   neopustí stroj:

   ```powershell
   $cert = New-SelfSignedCertificate -Subject "CN=<jmeno.prijmeni>-course-app" `
     -CertStoreLocation "Cert:\CurrentUser\My" `
     -KeyExportPolicy NonExportable -KeySpec Signature `
     -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256 `
     -NotAfter (Get-Date).AddMonths(6)
   Export-Certificate -Cert $cert -FilePath .\course-app.cer   # JEN verejna cast
   ```

   **Kde certifikát najdu — úložiště certifikátů.** Windows má úložiště dvojí:
   **uživatele** (`certmgr.msc`, PowerShellem `Cert:\CurrentUser\...`) a **počítače**
   (`certlm.msc`, `Cert:\LocalMachine\...`, vyžaduje admin — sem patří certy pro
   scheduled tasky, viz D3). Právě vygenerovaný cert leží v uživatelském úložišti,
   složka *Osobní* — pozor, PowerShell jí říká `My` (`Cert:\CurrentUser\My`).
   Úložiště je v PowerShellu obyčejný „disk":

   ```powershell
   Get-ChildItem Cert:\CurrentUser\My |
     Where-Object Subject -like "*course-app*" |
     Select-Object Subject, Thumbprint, NotAfter
   ```

   Ověřte si oboje cesty: v `certmgr.msc` dvojklikem na cert („Máte privátní klíč,
   který odpovídá tomuto certifikátu") a zkuste pravý klik → *Všechny úkoly →
   Exportovat* — volba „exportovat privátní klíč" je šedivá. To je `NonExportable`
   v akci; k tématu se vrátí odpolední blok
   [`../certificates-and-keys/`](../certificates-and-keys/) i YubiKey demo.

3. **Nahrát veřejnou část** (`.cer`) na app registraci (Certificates & secrets →
   Certificates → Upload). Soubor `.cer` je jediné, co stroj opouští — žádný `.pfx`,
   žádný private key, nic do repa (ověř `.gitignore`).
4. **Demonstrace priming promptu, verze 1 → 2**: než napíšete připojení sami, zkuste
   Test B z [`../../day-1/formats-fundamentals/copilot-priming-prompt.md`](../../day-1/formats-fundamentals/copilot-priming-prompt.md) —
   s verzí 1 nechte Copilota navrhnout PnP připojení (typicky vygeneruje
   `Connect-PnPOnline` **bez `-ClientId`** — spadlo by), pak v nové konverzaci
   s verzí 2 totéž (návrh je správně). Od teď používáte verzi 2.
5. **App-only přihlášení** bez promptu:

   ```powershell
   Connect-PnPOnline -Url "https://<tenant>-admin.sharepoint.com" `
     -ClientId $clientId -Tenant "<tenant>.onmicrosoft.com" `
     -Thumbprint $cert.Thumbprint
   ```

   **Hned po připojení ověřte, že jste připojení a kdo jste** (návyk na celý kurz):

   ```powershell
   # 1. Detaily připojení — kam a jak jsem připojený
   Get-PnPConnection | Select-Object Url, ConnectionType, ClientId, Tenant

   # 2. Základní analýza tokenu
   $t = Get-PnPAccessToken -ResourceTypeName SharePoint -Decoded
   $t.Audiences                                    # očekáváte: https://<tenant>.sharepoint.com
   $t.Claims | Where-Object Type -in 'roles','scp','upn','app_displayname' |
     Select-Object Type, Value                     # app-only = roles, žádné upn

   # 3. Reálné volání — teprve tohle je důkaz
   Get-PnPTenantSite | Select-Object -First 3
   ```

   Alternativa nezávislá na verzi modulu (a názorná — payload tokenu je obyčejný JSON):
   ruční dekódování, viz [`README.md`](README.md) sekce „Ověření stavu připojení".

   Když cokoli selže (`Unauthorized`, `AADSTS…`, „not of type RSA"), postupujte podle
   [`troubleshooting-auth.md`](troubleshooting-auth.md) — pokrývá typické scénáře
   včetně pasti Delegated vs. Application permission.

6. **Skriptem vytvořit tři pracovní weby** dle naming konvence — parametrizovaně, ne
   copy-paste třikrát:

   ```powershell
   foreach ($stage in 'dev','test','prod') {
     New-PnPSite -Type CommunicationSite `
       -Title "<jmeno-prijmeni> $stage" `
       -Url "https://<tenant>.sharepoint.com/sites/<jmeno-prijmeni>-$stage"
   }
   ```

7. **Zabalit do `Connect-CourseTarget` wrapperu**: funkce s parametry
   `-Module (PnP|Graph|SPO)` a `-AuthMode (Interactive|DeviceCode|Certificate)`, uvnitř
   mapování na správný `Connect-*` cmdlet, strukturovaný log každého připojení (timestamp,
   modul, auth mode, výsledek — objekt/JSON, ne `Write-Host`). Certificate větev právě
   ověřena kroky 5-6; Interactive/DeviceCode doplnit a otestovat.

## Ověření

- [ ] Certifikát existuje v `Cert:\CurrentUser\My` s `NonExportable` klíčem; v pracovní
      složce ani repu není žádný `.pfx`/private key.
- [ ] App registrace má nahranou veřejnou část certifikátu a přihlášení kroku 5 proběhne
      **bez jakéhokoli interaktivního promptu**.
- [ ] Účastník ověřil připojení všemi třemi úrovněmi (connection → token → reálné
      volání) a umí v tokenu ukázat `roles` a vysvětlit, proč chybí `upn`.
- [ ] Existují weby `-dev`, `-test`, `-prod` dle naming konvence, vytvořené skriptem
      (ne ručně v UI).
- [ ] `Connect-CourseTarget` funguje minimálně pro kombinace PnP+Certificate a
      PnP+Interactive a loguje strukturovaně.
- [ ] Import modulů jde přes `-RequiredVersion` (pin z předpokladů).

## Fallback

- Pokud `New-PnPSite` v app-only režimu selže (tenant policy), vytvořit weby pod delegated
  přihlášením (`-Interactive`) a app-only certifikátovou cestu ověřit na čtecí operaci
  (`Get-PnPSite`) — cíl labu (cert bez promptu) zůstává splněn.
- Pokud se týž den nestihne krok 7, Interactive/DeviceCode větve wrapperu se doplní jako
  domácí rozcvička před D3 — Graph lab a mini-capstone v D3 wrapper předpokládají.
