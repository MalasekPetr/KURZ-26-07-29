# Certifikáty a klíče v praxi: CER/PEM/PFX, stores, YubiKey

> Typ: povinný · Den: 2 · Odhad: 45 min výklad + 30 min živé demo (YubiKey)

## Cíle
- Rozlišit soubory, které kolem certifikátů kolují: `.cer`/DER, `.pem`, `.pfx`/PKCS#12 —
  co která přípona obsahuje a co z toho smí opustit stroj.
- Orientovat se ve Windows cert store (CurrentUser vs LocalMachine, `Cert:\` drive)
  a vědět, jak certifikáty žijí na Linux/macOS (soubory PEM místo store).
- Vysvětlit, co řeší hardware klíč (YubiKey): privátní klíč, který **fyzicky nejde
  zkopírovat** — a kdy taková investice dává smysl.
- Zařadit si možnosti do žebříčku: client secret → software cert → hardware
  klíč / managed identity / Key Vault.

## Výklad

### Pár klíčů — jediné, o co tu jde
Certifikátová autentizace stojí na dvojici klíčů: **privátní** (prokazuji se, nesmí
opustit můj stroj) a **veřejný** (ověřuje mě protistrana, může ho mít kdokoli).
Certifikát je veřejný klíč + metadata (kdo, dokdy platí, otisk/thumbprint). App
registrace v Entra dostane **jen veřejnou část** — proto ranní Lab 3 nahrával `.cer`
a nikdy `.pfx`.

### Formáty souborů — co je uvnitř
| Soubor | Obsah | Smí opustit stroj? |
|---|---|---|
| `.cer` / `.crt` (DER = binární, nebo Base64) | jen veřejný klíč + metadata | ano — tohle se nahrává do Entra |
| `.pem` | textová obálka `-----BEGIN...-----`; může nést certifikát, privátní klíč, nebo oboje | podle obsahu! `BEGIN PRIVATE KEY` = nikdy |
| `.pfx` / `.p12` (PKCS#12) | certifikát **včetně privátního klíče**, chráněný heslem | jen řízený přenos (import na server), nikdy mail/chat/repo |

PEM je jen Base64 zápis s hlavičkou — tentýž certifikát může existovat jako `.cer` i
`.pem`; na Windows převažuje DER/PFX + cert store, na Linux/macOS (a v kontejnerech,
viz [`../../day-3/azure-orientation/`](../../day-3/azure-orientation/)) se pracuje
se soubory PEM. PowerShell 7 je multiplatformní — proto je dobré znát obojí.

### Windows cert store
`Cert:\CurrentUser\My` (můj profil) vs `Cert:\LocalMachine\My` (celý stroj — potřebuje
ho scheduled task pod servisním účtem; vracíme se k tomu v
[`../../day-3/azure-orientation/`](../../day-3/azure-orientation/)). Prohlížení:
`certmgr.msc` / `certlm.msc`, nebo PowerShellem `Get-ChildItem Cert:\CurrentUser\My`.
Klíčový atribut z Labu 3: **NonExportable** — privátní klíč vygenerovaný tak, že ho
Windows odmítne exportovat; první stupeň ochrany proti zkopírování.

### Hardware klíč — NonExportable dotažený do konce
`NonExportable` ve Windows store je softwarová pojistka — admin stroje ji umí obejít.
**YubiKey (PIV)** posouvá privátní klíč do vyhrazeného čipu: klíč se vygeneruje přímo
na tokenu a **neexistuje způsob, jak ho dostat ven** — podpis se provádí uvnitř čipu,
volitelně po zadání PIN a fyzickém dotyku. Windows minidriver certifikát z klíče
promítne do cert store, takže `Connect-PnPOnline -Thumbprint ...` funguje beze změny —
jen podpis proběhne v hardwaru. Stejný princip: smart card (PIV je protokol čipových
karet), HSM, a v cloudu **Azure Key Vault** (HSM jako služba — klíč automatizace,
která neběží na vašem stolku, ale v Azure).

Kdy investovat: credential s vysokými právy používaný člověkem (admin, konzultant
s přístupem do více tenantů) → hardware klíč; bezobslužná automatizace v Azure →
managed identity / Key Vault; běžný scheduled task on-prem → software cert
v LocalMachine store s NonExportable.

## Demo (instruktor, živě)
Viz [`demo-yubikey.md`](demo-yubikey.md) — vygenerování klíče na YubiKey, self-signed
certifikát, upload `.cer` na app registraci z Labu 3 a `Connect-PnPOnline` s dotykem
klíče. Účastníci hands-on jen softwarové certy (Lab 3); klíče jsou instruktorské.

## Klíčové rozlišení
- **Privátní vs veřejný klíč** — privátní se prokazuje a nikam nechodí; veřejný ověřuje
  a smí kamkoli. Všechna pravidla tohoto bloku jsou důsledkem této věty.
- **`.cer` vs `.pfx`** — bez privátního klíče vs s ním; do Entra jde vždy jen `.cer`.
- **DER/CER vs PEM** — binární Windows svět vs textový soubor pro Linux/kontejnery;
  tentýž obsah, jiný obal.
- **NonExportable (software) vs hardware klíč** — pojistka v OS vs fyzikální nemožnost;
  mezistupně nejsou selhání, jsou to úrovně podle hodnoty credentialu.
- **MFA token vs PIV credential** — tentýž YubiKey umí obojí, ale onboarding D1 ho
  potkal jako MFA pro člověka; tady drží credential aplikace.

## Zdroje (Microsoft / Yubico)
- [Certificate credentials for application authentication](https://learn.microsoft.com/en-us/entra/identity-platform/certificate-credentials)
- [about_Certificate_Provider (PowerShell)](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.security/about/about_certificate_provider)
- [Yubico PIV Tool / ykman](https://developers.yubico.com/yubikey-manager/)
- [Azure Key Vault keys overview](https://learn.microsoft.com/en-us/azure/key-vault/keys/about-keys)

## Stav produktu / delta
> [!WARNING]
> Ověřit k datu běhu — verze YubiKey Manager (`ykman`) a minidriver chování na
> Windows 11 se mění; demo projet na učebním stroji den před během (viz go/no-go
> v [`instructor-notes.md`](instructor-notes.md)).
