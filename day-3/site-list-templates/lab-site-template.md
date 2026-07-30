# Lab · Vlastní site template od naklikání ke kódu

> Odhad: 30 min · Režim: živý tenant, vlastní weby `-dev` / `-test`

## Cíl

Vyrobit vlastní site template, aniž byste JSON psali od nuly: strukturu naklikat na
`-dev` webu, vygenerovat z ní site script, upravit, zaregistrovat a **aplikovat na
`-test` web** — a ověřit, že cílový web vypadá jako zdrojový.

## Předpoklady

- Weby `-dev` a `-test` z Labu 3 (D2), připojení PnP (delegated stačí).
- Účastník je Global/SharePoint administrator (registrace šablon je tenant-wide akce —
  platí pravidla z [`../../day-1/onboarding/ways-of-working.md`](../../day-1/onboarding/ways-of-working.md)).

## Kroky

1. **Naklikat vzor** na `-dev` webu: nový seznam `Žádanky` se sloupci `Středisko` (text,
   povinný) a `Stav` (choice: Nová / Schválená / Zamítnutá). Diakritiku použijte
   záměrně — poteče celým labem.
2. **Vygenerovat script z hotového webu** — nejrychlejší cesta k JSONu:

   ```powershell
   Get-PnPSiteScriptFromWeb -Url "https://<tenant>.sharepoint.com/sites/<jmeno-prijmeni>-dev" `
     -Lists "Lists/Žádanky" | Out-File .\zadanky.json -Encoding utf8
   ```

   Otevřít v VS Code, přečíst: najít `createSPList`, `addSPField`, `choices` — je to
   tentýž JSON, který umíte od D1.
3. **Upravit**: přidat `setDescription` k seznamu a doplnit jednu volbu do `Stav`
   (např. `Vráceno k doplnění`). Zkontrolovat, že soubor zůstal UTF-8.
4. **Zaregistrovat** script i template:

   ```powershell
   $json = Get-Content .\zadanky.json -Raw -Encoding utf8
   $s = Add-PnPSiteScript -Title "<jmeno-prijmeni> Žádanky" -Content $json
   $d = Add-PnPSiteDesign -Title "<jmeno-prijmeni> Web oddělení" `
          -SiteScriptIds $s.Id -WebTemplate CommunicationSite
   ```

   Pozor na naming konvenci s vlastním prefixem — v tenantu je vás 25 a limit je 100.
5. **Aplikovat na `-test` web** a ověřit výsledek:

   ```powershell
   Invoke-PnPSiteDesign -Identity $d.Id -WebUrl "https://<tenant>.sharepoint.com/sites/<jmeno-prijmeni>-test"
   Connect-PnPOnline -Url ".../<jmeno-prijmeni>-test" -ClientId $clientId -Interactive
   Get-PnPField -List "Žádanky" | Where-Object Hidden -eq $false |
     Select-Object Title, InternalName, TypeAsString
   (Get-PnPField -List "Žádanky" -Identity "Stav").Choices
   ```

6. **Porovnat** `-dev` a `-test`: sedí sloupce, typy i volby? Diakritika v názvech je
   v pořádku? Co šablona **nepřenesla** (data v seznamu, oprávnění) — zapsat jednou
   větou; je to hranice mezi site template a PnP šablonou.

## Ověření

- [ ] `zadanky.json` je čitelný JSON v UTF-8 a účastník umí ukázat, která akce vytváří
      seznam a která sloupec.
- [ ] Site script i site template jsou zaregistrované pod vlastním prefixem.
- [ ] Web `-test` má seznam `Žádanky` se všemi sloupci včetně doplněné volby a správnou
      diakritikou.
- [ ] Účastník umí pojmenovat aspoň dvě věci, které šablona nepřenesla.

## Úklid (nutný)

Limit je 100 scriptů/templates na tenant a jsme v něm všichni — po ověření smažte
svoje registrace, ať zbyde místo pro ostatní:

```powershell
Remove-PnPSiteDesign -Identity $d.Id -Force
Remove-PnPSiteScript -Identity $s.Id -Force
```

## Fallback

Pokud registrace šablon v tenantu selže (policy/limit), lab dokončete do kroku 3 —
vygenerovaný a upravený JSON je plnohodnotný výstup; aplikaci předvede lektor na svém
webu a účastníci porovnají výsledek s vlastním JSONem.
