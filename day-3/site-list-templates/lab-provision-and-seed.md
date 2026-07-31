# Lab · Od webu k obsahu: web, seznam, položky a dokumenty

> Odhad: 45 min · Režim: živý tenant, vlastní nový web

## Cíl

Postavit skriptem celý malý celek od nuly: **web → seznam se správně navrženými sloupci
→ dávkově naplněné položky → nahrané dokumenty s metadaty** — a ověřit výsledek. Všechno
doporučenými postupy: interní názvy bez diakritiky, index dřív než data, dávkový zápis,
`try/catch`, UTF-8.

## Předpoklady

- Funkční připojení z D2 (Lab 3): `-ClientId`, certifikát nebo `-Interactive`.
- Naming konvence z [`../../day-1/onboarding/ways-of-working.md`](../../day-1/onboarding/ways-of-working.md)
  — web pojmenovat `<jmeno-prijmeni>-projekt`.
- Vstupní data: JSON s položkami a 2–3 malé dokumenty. Vygenerujte si je Copilotem dle
  [`../../day-1/formats-fundamentals/guide-dummy-data.md`](../../day-1/formats-fundamentals/guide-dummy-data.md)
  (fiktivní data, česká diakritika).

## Kroky

### 1. Web (3 min)

```powershell
Connect-PnPOnline -Url "https://<tenant>-admin.sharepoint.com" -ClientId $clientId -Interactive

$siteUrl = "https://<tenant>.sharepoint.com/sites/<jmeno-prijmeni>-projekt"
New-PnPSite -Type CommunicationSite -Title "<Jméno> Projekt" -Url $siteUrl -ErrorAction Stop
Connect-PnPOnline -Url $siteUrl -ClientId $clientId -Interactive
```

### 2. Seznam a sloupce — interní názvy vědomě (12 min)

Nejdůležitější návyk celého labu: **`-InternalName` bez diakritiky a mezer,
`-DisplayName` jak chcete.** Interní název už nikdy nezměníte a používají ho všechny
dotazy; zobrazovaný název přejmenujete kdykoli.

```powershell
New-PnPList -Title "Žádanky" -Template GenericList -EnableVersioning -ErrorAction Stop

Add-PnPField -List "Žádanky" -DisplayName "Středisko" -InternalName "Stredisko" `
  -Type Text -AddToDefaultView -Required
Add-PnPField -List "Žádanky" -DisplayName "Stav" -InternalName "Stav" `
  -Type Choice -Choices "Nová","Schválená","Zamítnutá" -AddToDefaultView
Add-PnPField -List "Žádanky" -DisplayName "Termín" -InternalName "Termin" `
  -Type DateTime -AddToDefaultView

# Index na sloupec, podle kterého budeme filtrovat — DOKUD JE SEZNAM PRÁZDNÝ
Set-PnPField -List "Žádanky" -Identity "Stav" -Values @{ Indexed = $true }

# Kontrola: display vs internal name
Get-PnPField -List "Žádanky" | Where-Object Hidden -eq $false |
  Select-Object Title, InternalName, TypeAsString
```

### 3. Položky dávkově (10 min)

Nikdy ne „položka za položkou" ve smyčce — jedno volání na 50 položek místo padesáti
volání (a méně throttlingu):

```powershell
$data = Get-Content .\zadanky.json -Raw -Encoding utf8 | ConvertFrom-Json

try {
    $batch = New-PnPBatch
    foreach ($r in $data) {
        Add-PnPListItem -List "Žádanky" -Values @{
            Title      = $r.nazev
            Stredisko  = $r.stredisko      # POZOR: interní názvy, ne zobrazované!
            Stav       = $r.stav
            Termin     = [datetime]$r.termin
        } -Batch $batch
    }
    Invoke-PnPBatch -Batch $batch -ErrorAction Stop
}
catch {
    Write-Error "Zápis položek selhal: $($_.Exception.Message)"
}
```

### 4. Dokumenty s metadaty (12 min)

```powershell
# Vlastní sloupec i v knihovně — ať dokumenty nesou metadata, ne jen název souboru
Add-PnPField -List "Documents" -DisplayName "Středisko" -InternalName "Stredisko" `
  -Type Text -AddToDefaultView

Add-PnPFolder -Name "Smlouvy" -Folder "Shared Documents" -ErrorAction SilentlyContinue

Get-ChildItem .\dokumenty\*.docx | ForEach-Object {
    Add-PnPFile -Path $_.FullName -Folder "Shared Documents/Smlouvy" `
      -Values @{ Stredisko = "Ekonomický odbor" } -ErrorAction Stop
}
```

**Proč `Add-PnPFile`**: sám volí správný způsob nahrání podle velikosti souboru (u velkých
používá chunkovaný upload) — ruční „poslat celý soubor jedním requestem" u větších
dokumentů selže na limitu.

### 5. Ověření (8 min)

```powershell
# Kolik toho tam je
Get-PnPList -Identity "Žádanky" | Select-Object Title, ItemCount

# Filtr na serveru, podle INDEXOVANÉHO sloupce, stránkovaně
$caml = "<View><Query><Where><Eq><FieldRef Name='Stav'/>" +
        "<Value Type='Text'>Nová</Value></Eq></Where></Query><RowLimit>100</RowLimit></View>"
Get-PnPListItem -List "Žádanky" -Query $caml |
  ForEach-Object { $_.FieldValues.Title }

# Dokumenty i s metadaty
Get-PnPListItem -List "Documents" -PageSize 100 |
  Select-Object @{n='Soubor';e={$_.FieldValues.FileLeafRef}},
                @{n='Středisko';e={$_.FieldValues.Stredisko}}

# Report pro člověka (Excel-friendly, D1)
Get-PnPListItem -List "Žádanky" -PageSize 500 |
  Select-Object @{n='Nazev';e={$_.FieldValues.Title}},
                @{n='Stredisko';e={$_.FieldValues.Stredisko}},
                @{n='Stav';e={$_.FieldValues.Stav}} |
  Export-Csv .\zadanky-report.csv -Encoding utf8BOM -UseCulture -NoTypeInformation
```

## Ověření

- [ ] Web `<jmeno-prijmeni>-projekt` existuje a byl vytvořen skriptem, ne v UI.
- [ ] Seznam `Žádanky` má sloupce s **interními názvy bez diakritiky** a účastník umí
      vysvětlit, proč na tom záleží.
- [ ] Sloupec `Stav` je indexovaný a index vznikl **před** naplněním dat.
- [ ] Položky byly vloženy **jedním dávkovým voláním**, ne ve smyčce po jedné.
- [ ] V knihovně jsou dokumenty a nesou hodnotu vlastního sloupce `Středisko`.
- [ ] CSV report je čitelný v Excelu včetně diakritiky.

## Úklid

```powershell
# Web do koše (obnovitelný ~93 dní; nové pokusy = nové číslo dle naming konvence)
Connect-PnPOnline -Url "https://<tenant>-admin.sharepoint.com" -ClientId $clientId -Interactive
Remove-PnPTenantSite -Url $siteUrl -Force
```

## Fallback

Pokud `New-PnPSite` v tenantu selže (policy), použijte existující web `-test` z D2 —
kroky 2–5 jsou beze změny. Pokud nemáte dokumenty po ruce, stačí libovolné soubory
z vlastní složky; obsah není podstatný, metadata jsou.

## Best practices, které jste právě použili

| Postup | Proč |
|---|---|
| `-InternalName` bez diakritiky, `-DisplayName` pěkně | interní název je navždy, používají ho všechny dotazy |
| index před naplněním dat | na velkém seznamu se index zavádí těžko ([`../graph-fundamentals/explainer-large-lists.md`](../graph-fundamentals/explainer-large-lists.md)) |
| `New-PnPBatch` / `Invoke-PnPBatch` | jedno volání místo N, méně throttlingu |
| `Add-PnPFile` s `-Values` | správný upload podle velikosti + metadata v jednom kroku |
| filtr CAML na indexovaném sloupci + `RowLimit` | dotaz nikdy nepřekročí threshold |
| `-ErrorAction Stop` + `try/catch` | chyba se pozná hned a čitelně |
| `-Encoding utf8` / `utf8BOM` | česká data přežijí cestu do Excelu |
