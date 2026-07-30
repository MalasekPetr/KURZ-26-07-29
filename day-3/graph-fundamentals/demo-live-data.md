# Živá dema · Přístup k datům M365 — od dotazu k reportu

Zásobník krátkých, spustitelných příkladů pro výklad i vlastní zkoušení. Každý blok má
**dotaz** a **co si všimnout** — pointu, kterou si odnést. Vše čtecí (kromě poslední
sekce), takže se nedá nic rozbít.

Vpravo vždy PnP ekvivalent: tentýž výsledek, jiná obálka nad tímtéž API — praktický
důkaz věty „moduly jsou wrappery" z D1.

## 1. Uživatelé — `$select`, `$filter`, `$top`

```powershell
# Graph
Invoke-MgGraphRequest GET "https://graph.microsoft.com/v1.0/users?`$select=displayName,mail,jobTitle&`$top=5"

# PnP (SPO pohled na uživatele webu)
Get-PnPUser | Select-Object Title, Email, LoginName -First 5
```

**Všimněte si:** v PowerShellu se `$` v URL musí escapovat (`` `$select ``) — jinak ho
PowerShell považuje za proměnnou a pošle prázdno. Klasická past prvního dne s Graphem.

## 2. Weby a jejich obsah

```powershell
# Najít web podle názvu
Invoke-MgGraphRequest GET "https://graph.microsoft.com/v1.0/sites?search=dev"

# Seznamy na webu (Graph potřebuje site-id — jak ho zjistit: tips-spo-api.md)
Invoke-MgGraphRequest GET "https://graph.microsoft.com/v1.0/sites/$siteId/lists?`$select=id,displayName,list"

# PnP — po Connect-PnPOnline na konkrétní web
Get-PnPList | Where-Object Hidden -eq $false | Select-Object Title, ItemCount, LastItemUserModifiedDate
```

**Všimněte si:** `ItemCount` a `LastItemUserModifiedDate` jsou zlato pro inventury —
„co je mrtvé" zjistíte jedním dotazem (a je to úloha 1 z capstone).

## 3. Položky seznamu — a past s `fields`

```powershell
# Graph: bez $expand=fields dostanete jen metadata položky, ne obsah sloupců!
Invoke-MgGraphRequest GET "https://graph.microsoft.com/v1.0/sites/$siteId/lists/$listId/items?`$expand=fields&`$top=10"

# PnP
Get-PnPListItem -List "Dokumenty" -PageSize 100 |
  ForEach-Object { $_.FieldValues.FileLeafRef }
```

**Všimněte si:** Graph vrací hodnoty sloupců až s `$expand=fields`; PnP je má
ve `FieldValues` jako hashtable — a klíče jsou **interní názvy polí**
([`tips-spo-api.md`](tips-spo-api.md)).

## 4. Soubory v knihovně

```powershell
# Graph: obsah korenové složky knihovny
Invoke-MgGraphRequest GET "https://graph.microsoft.com/v1.0/sites/$siteId/drive/root/children?`$select=name,size,lastModifiedDateTime"

# PnP: rekurzivně přes knihovnu
Get-PnPFolderItem -FolderSiteRelativeUrl "Shared Documents" -ItemType File |
  Select-Object Name, Length, TimeLastModified
```

**Všimněte si:** knihovna = `drive` v Graphu, `list` v SPO světě — tentýž objekt dvěma
jmény. Pro práci se soubory je Graph `drive` API pohodlnější, pro metadata a sloupce
naopak `lists`.

## 5. Vyhledávání napříč tenantem

```powershell
# PnP — nejkratší cesta k „najdi mi všechno, co obsahuje X"
Submit-PnPSearchQuery -Query "smlouva" -MaxResults 10 |
  Select-Object -ExpandProperty ResultRows |
  ForEach-Object { $_.Title; $_.Path }
```

**Všimněte si:** search respektuje oprávnění volajícího — pod app-only identitou uvidíte
jiné výsledky než pod svým účtem. Dobrý test „co ta aplikace vlastně vidí".

## 6. Uživatelé, skupiny, členství (Graph)

```powershell
Invoke-MgGraphRequest GET "https://graph.microsoft.com/v1.0/groups?`$filter=startswith(displayName,'HR')&`$select=id,displayName,visibility"
Invoke-MgGraphRequest GET "https://graph.microsoft.com/v1.0/groups/$groupId/members?`$select=displayName,userPrincipalName"
```

**Všimněte si:** tohle je zdroj pro report „kdo má k čemu přístup" — a zároveň důvod,
proč aplikace s `Group.Read.All` vidí celý adresář. Least privilege v praxi (D2).

## 7. Report do CSV — uzavření kruhu s D1

```powershell
Get-PnPList | Where-Object Hidden -eq $false |
  Select-Object Title, ItemCount, LastItemUserModifiedDate |
  Sort-Object LastItemUserModifiedDate |
  Export-Csv .\inventura-webu.csv -Encoding utf8BOM -UseCulture -NoTypeInformation
```

**Všimněte si:** `utf8BOM` + `-UseCulture` = report, který se v českém Excelu otevře
správně (diakritika i středníky). Přesně to pravidlo z D1.

## 8. Zápis — jen nad vlastním `-dev` webem

```powershell
Add-PnPListItem -List "Zapisy" -Values @{ Title = "Testovací položka"; Status = "Návrh" }
Set-PnPListItem -List "Zapisy" -Identity 1 -Values @{ Status = "Schváleno" }

# Dávkově — jeden roundtrip místo N
$batch = New-PnPBatch
1..50 | ForEach-Object { Add-PnPListItem -List "Zapisy" -Values @{ Title = "Řádek $_" } -Batch $batch }
Invoke-PnPBatch -Batch $batch
```

**Všimněte si:** hodnoty choice polí musí odpovídat povoleným možnostem (jak je zjistit:
[`tips-spo-api.md`](tips-spo-api.md)); a dávka je rozdíl mezi „50 volání" a „jedno" —
u tisíců položek rozdíl mezi minutami a hodinami.

## Jak s tímto souborem pracovat

Nečtěte to jako lineární text — je to **zásobník vzorů**. Když v capstone (Lab 5) nebo
v praxi potřebujete „nějak se dostat k datům", najděte nejbližší příklad, zkopírujte
a upravte. A dejte ho do promptu, když si necháváte skript generovat Copilotem: model
pak staví na ověřeném vzoru, ne na fantazii.

## Zdroje (Microsoft)

- [Use query parameters (Graph)](https://learn.microsoft.com/en-us/graph/query-parameters)
- [Working with SharePoint sites in Graph](https://learn.microsoft.com/en-us/graph/api/resources/sharepoint)
- [PnP PowerShell cmdlets](https://pnp.github.io/powershell/cmdlets/)
- [Invoke-MgGraphRequest](https://learn.microsoft.com/en-us/powershell/microsoftgraph/authentication-commands)
