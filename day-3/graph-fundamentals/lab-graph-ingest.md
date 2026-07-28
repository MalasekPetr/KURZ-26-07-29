# Lab 4 · Čtení dat Graphem: Explorer → skript se stránkováním

> Odhad: 60 min · Režim: živý tenant

## Cíl

Účastník složí Graph dotaz nejdřív v Graph Exploreru, pak ho přenese do PowerShell
skriptu, který korektně stránkuje (`@odata.nextLink`) a základně rozlišuje chyby
(retry vs fail-fast).

## Předpoklady

- `Connect-CourseTarget` wrapper z [`../../day-2/powershell-deep-dive/`](../../day-2/powershell-deep-dive/), app registrace s `Sites.Read.All` (Graph, delegated).
- Kurzový tenant obsahuje dostatek uživatelů/webů, aby dotaz reálně vyžadoval stránkování.
- Přihlášený M365 Copilot Chat (workflow z D1: navrhne → čtu → testuji).

## Kroky

1. **Graph Explorer**: složit dotaz na seznam webů/uživatelů s `$select` a `$filter`,
   ověřit odpověď; záložka permissions — proč stačí `Sites.Read.All`. Snížit `$top` na 5
   a najít v odpovědi `@odata.nextLink` — takhle vypadá stránkování.
2. **Přenést dotaz do skriptu**: `Invoke-MgGraphRequest` s touž URL; ověřit, že vrací
   jen první stránku — a že skript bez smyčky **tiše lže**.
3. Napsat funkci `Get-AllGraphResults` (návrh klidně z Copilot Chatu, číst před
   spuštěním), která projde `@odata.nextLink` až do konce a vrátí kompletní kolekci.
   Ověřit počtem proti Graph Exploreru.
4. Přidat základní klasifikaci chyb: 429 → počkat `Retry-After`, pak retry; 5xx →
   exponenciální backoff (max. N pokusů); jiné 4xx → vyhodit chybu okamžitě, bez retry.
   Otestovat nad simulovaným 429 response logem od instruktora.
5. Zalogovat každý pokus strukturovaně (logging vzor z [`../../day-2/powershell-deep-dive/`](../../day-2/powershell-deep-dive/)).

## Ověření

- [ ] Účastník umí v Graph Exploreru vysvětlit každou část svého dotazu (endpoint,
      `$select`, `$filter`, `$top`).
- [ ] Skript vrátí kompletní dataset i při datasetu větším než jedna stránka Graph
      odpovědi (počet sedí s Explorerem).
- [ ] Retry logika čte `Retry-After` z odpovědi, ne pevnou hodnotu (`Start-Sleep -Seconds 30`
      neprojde).
- [ ] Log obsahuje rozlišení retry (429/5xx) vs fail-fast (jiné 4xx).

## Bonus (jen při rezervě)

Uměle vyvolat skutečný throttling (vysoký počet rychlých requestů) a ověřit chování
naživo — nepovinné, simulovaný log z kroku 4 stačí.

## Fallback

Pokud tenant nemá dost objektů na reálné stránkování, doplní lektor demo data; pro krok 4 je
simulovaný 429 log připraven vždy — reálné vyvolání throttlingu je jen bonus.
