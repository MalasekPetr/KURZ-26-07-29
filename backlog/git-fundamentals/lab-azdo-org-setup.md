# Lab B · Vlastní Azure DevOps organizace s Entra ID

> Odhad: 45 min · Režim: živý (vlastní/kurzovní Entra tenant)

## Cíl

Založit Azure DevOps organizaci tak, jak by vznikala doma: napojenou na Entra ID,
v EU regionu, s projektem, repozitářem a branch policy, která chrání `main`. Výstup
labu je zároveň sdílené repo pro [Lab A](lab-branch-pr-conflict.md).

## Předpoklady

- Účet v Entra tenantu (kurzovní účet stačí).
- Doporučení a srovnání hostingů:
  [`explainer-git-hosting.md`](../../day-1/vscode-copilot-env/explainer-git-hosting.md).

## Kroky

1. **Založit organizaci**: `https://dev.azure.com` → *New organization* — při zakládání
   zvolit **region Europe** a ověřit, že organizace je **connected k vašemu Entra
   tenantu** (Organization settings → Microsoft Entra) — ne k osobnímu Microsoft účtu.
2. **Projekt + repo**: nový projekt (private), v něm Repos → inicializovat repo.
3. **Naklonovat a nahrát obsah**: `git clone`, přidat `report.ps1` (kostru pro Lab A)
   a `.gitignore` (minimálně `*.pfx`, `*credentials*`), commit, push.
4. **Branch policy na `main`**: Repos → Branches → `main` → Branch policies:
   - *Require a minimum number of reviewers* = 1 (zakázat self-approve),
   - *Check for linked work items* = optional (zmínit, k čemu je).
   Ověřit: přímý `git push` do `main` teď server odmítne — změny jen přes PR.
5. **Přizvat kolegu** (druhého z dvojice pro Lab A): Project settings → Teams/Permissions,
   role Contributor. Všimnout si, že pozvánka jde přes Entra identitu — žádný nový účet.
6. **Volitelně**: Boards → založit work item „Přidat filtr do reportu" a v Labu A ho
   svázat s PR (`#ID` v popisu) — ochutnávka toho, proč je Repos+Boards v jednom užitečné.

## Ověření

- [ ] Organizace je připojená k Entra tenantu (ne k osobnímu MS účtu) a leží v EU regionu.
- [ ] Přímý push do `main` je zamítnut branch policy; PR s 1 reviewerem projde.
- [ ] Druhý člen dvojice má přístup přes svou Entra identitu (žádné nové heslo).
- [ ] `.gitignore` blokuje `*.pfx` a credential soubory od prvního commitu.

## Fallback

Pokud zakládání organizací blokuje tenant policy, lektor předpřipraví jednu organizaci
a každá dvojice dostane vlastní projekt — kroky 2–6 se nemění.
