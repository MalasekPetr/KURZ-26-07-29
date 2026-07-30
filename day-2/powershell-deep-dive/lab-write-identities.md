# Mini-lab · Tři podpisy zápisu: UI vs. delegated vs. app-only

> Odhad: 25 min · Režim: živý tenant, zápis **jen na vlastní `-dev` web**

## Cíl

Zapsat do téhož SharePoint seznamu třemi cestami — ručně přes UI, skriptem
s **delegated** přihlášením a skriptem **app-only** — a na sloupci „Vytvořil"
na vlastní oči vidět, jak se každá cesta podepisuje. Tohle je vizuální důkaz
celé identity osy D2: kdo zapsal, je v SPO navždy vidět.

## Předpoklady

- Dokončený Lab 3: web `-dev`, certifikát, funkční app-only připojení.
- App registrace z Labu 2 s application permission `Sites.FullControl.All`
  (SharePoint) z Labu 3.

## Kroky

1. **Připravit seznam**: na vlastním webu `-dev` založit seznam `Zapisy`
   (stačí UI: *New → List*, jen výchozí sloupec Title).
2. **Zápis přes UI**: v prohlížeči přidat položku s Title `Zápis přes UI`.
3. **Delegated oprávnění pro aplikaci**: app registrace zatím umí za uživatele jen
   číst (Graph `Sites.Read.All` z Labu 2) — přidejte *API permissions → SharePoint →
   **Delegated** → `AllSites.Write`* + admin consent, se zapsaným zdůvodněním
   (přesně tohle večer najde hardening audit). Všimněte si názvosloví: delegated
   se jmenuje `AllSites.Write`, application `Sites.FullControl.All` — dvě dlaždice,
   dvě jména (viz [`troubleshooting-auth.md`](troubleshooting-auth.md)).
4. **Zápis delegated** — přihlášený jste vy, aplikace jedná vaším jménem:

   ```powershell
   Connect-PnPOnline -Url "https://<tenant>.sharepoint.com/sites/<jmeno-prijmeni>-dev" `
     -ClientId $clientId -Interactive
   (Get-PnPAccessToken -ResourceTypeName SharePoint -Decoded).Claims |
     Where-Object Type -eq 'upn' | Select-Object -ExpandProperty Value    # = vy
   Add-PnPListItem -List "Zapisy" -Values @{ Title = "Zápis delegated" }
   ```

5. **Zápis app-only** — jedná aplikace sama za sebe:

   ```powershell
   Connect-PnPOnline -Url "https://<tenant>.sharepoint.com/sites/<jmeno-prijmeni>-dev" `
     -ClientId $clientId -Tenant "<tenant>.onmicrosoft.com" -Thumbprint $thumbprint
   (Get-PnPAccessToken -ResourceTypeName SharePoint -Decoded).Claims |
     Where-Object Type -in 'roles','upn' | Select-Object Type, Value   # role ano, upn ne
   Add-PnPListItem -List "Zapisy" -Values @{ Title = "Zápis app-only" }
   ```

6. **Porovnat podpisy** — v UI si do zobrazení přidejte sloupec **Vytvořil**
   (Created By), nebo skriptem:

   ```powershell
   Get-PnPListItem -List "Zapisy" -Fields Title, Author | ForEach-Object {
     [pscustomobject]@{
       Title   = $_.FieldValues.Title
       Vytvoril = $_.FieldValues.Author.LookupValue
     }
   }
   ```

   Očekávaný výsledek:

   | Title | Vytvořil |
   |---|---|
   | Zápis přes UI | Jan Novák |
   | Zápis delegated | Jan Novák |
   | Zápis app-only | **`<jmeno.prijmeni>-course-app`** |

7. **Vyvodit**: UI a delegated nesou **vaše** jméno (delegated = aplikace jedná vaším
   jménem a s vašimi právy — proto jste v kroku 4 viděli v tokenu `upn`); app-only nese
   **jméno aplikace** (v tokenu `roles`, žádný uživatel). Automatizace tedy v SPO
   zanechává vlastní, odlišitelnou a auditovatelnou stopu — argument „všechno je
   auditovatelné" z D1 právě přestal být teorie.

## Ověření

- [ ] Seznam `Zapisy` obsahuje tři položky se třemi různými kombinacemi podpisů
      dle tabulky (dvě s vaším jménem, jedna se jménem aplikace).
- [ ] Účastník umí u delegated i app-only zápisu ukázat odpovídající důkaz v tokenu
      (`upn` vs. `roles`).
- [ ] Delegated `AllSites.Write` má zapsané zdůvodnění pro večerní hardening audit.

## Fallback

Pokud delegated consent nebo `-Interactive` přihlášení selže (public client platforma,
viz [`troubleshooting-auth.md`](troubleshooting-auth.md)), proveďte jen UI + app-only
větev — hlavní kontrast (člověk vs. aplikace ve sloupci Vytvořil) zůstává; delegated
doplňte po vyřešení o přestávce.
