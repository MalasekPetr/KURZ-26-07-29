# Cvičení · První Graph dotazy v Graph Exploreru

> Odhad: 20 min · Režim: živý tenant, jen čtení, bez psaní kódu

## Cíl

Každý účastník si sám zavolá Microsoft Graph — uvidí, že „volat API" znamená složit
URL a přečíst JSON, nic víc. Zároveň si poprvé sáhne na permissions model: dotaz
funguje jen s oprávněním, které je vidět a dá se zdůvodnit.

## Předpoklady

- Účet z onboardingu (přihlášený v pracovním profilu Edge).
- `https://developer.microsoft.com/graph/graph-explorer` (consent zajišťuje lektor předem).

## Kroky

1. **Přihlásit se** v Graph Exploreru kurzovním účtem (tlačítko vpravo nahoře) —
   do té doby vrací dotazy demo data fiktivního tenantu, po přihlášení **vaše** data.
2. **`GET /me`** — spustit, přečíst JSON odpověď: najít `displayName`, `mail`, `id`.
   Tohle je celé API volání: sloveso + URL + token (schovaný za přihlášením) + JSON.
3. **`GET /me/joinedTeams`** — čí jsou to data? Kde v odpovědi je pole a kde objekt
   (vazba na dopolední JSON výklad — pět stavebních kamenů).
4. **Zúžit odpověď**: `GET /me?$select=displayName,jobTitle` — porovnat velikost
   odpovědi s krokem 2. `$select` je první „dospělý" návyk práce s API.
5. **Podívat se na permissions**: záložka *Modify permissions* u dotazu — jaké oprávnění
   dotaz potřeboval? Kdo ho schválil? (Odpověď: admin consent, přesně tohle budete
   v D2 dělat sami pro vlastní aplikaci.)
6. **Volitelně**: `GET /sites?search=*` — seznam webů tenantu; všimnout si, že odpověď
   má `value` pole — a co asi znamená, když je webů víc, než se vešlo? (Odpověď —
   stránkování — přijde v Labu 4, D3.)

## Ověření

- [ ] Účastník spustil minimálně dotazy z kroků 2–4 pod vlastním účtem.
- [ ] Umí říct, kde v odpovědi kroku 3 je pole a kde objekt.
- [ ] Umí říct, jaké permission použil dotaz `/me` a kde to zjistil.

## Fallback

Pokud přihlášení do Graph Exploreru selže (consent, síť), účastník pokračuje nad
demo tenantem Graph Exploreru (bez přihlášení) — kroky 2–4 fungují i tam, jen nad
fiktivními daty; krok 5 ukáže instruktor na plátně.
