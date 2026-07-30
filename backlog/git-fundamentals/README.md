# Git, GitHub a Azure DevOps do hloubky

> Typ: kandidát pro navazující běh (backlog) · Odhad: půlden — 75 min výklad + 60 min Lab A + 45 min Lab B

Samostatný modul rozvíjející základy z bloku
[`day-1/vscode-copilot-env`](../../day-1/vscode-copilot-env/) (a jeho
[`explainer-git-hosting.md`](../../day-1/vscode-copilot-env/explainer-git-hosting.md))
do plnohodnotného tréninku: reálná týmová práce s větvemi a pull requesty včetně
konfliktu, a založení vlastní Azure DevOps organizace naživo.

## Cíle
- Rozumět Gitu pod povrchem: co je commit (snímek + rodič), větev (pohyblivý ukazatel),
  `HEAD`, remote — model, ze kterého všechny příkazy plynou, místo memorování příkazů.
- Projít celý týmový cyklus ve dvojici: branch → commity → push → pull request →
  review → merge — včetně **záměrně vyvolaného konfliktu** a jeho vyřešení ve VS Code.
- Založit a nakonfigurovat **Azure DevOps organizaci** pro vlastní tým: projekt, repo,
  Entra ID přihlášení, branch policy na `main` (povinný PR + reviewer).
- Umět rozhodnout merge vs. rebase vs. squash — a vědět, proč pro malý tým stačí
  „merge + malé commity".

## Výklad (osnova)

### Model Gitu za 20 minut
Commit = snímek celého stromu + odkaz na rodiče (ne diff!); větev = pojmenovaný
ukazatel na commit, který se posouvá s každým novým commitem; `HEAD` = kde právě
stojím; remote = kopie grafu jinde. Z modelu plyne vše ostatní: `merge` spojuje dva
ukazatele novým commitem se dvěma rodiči, `pull` = `fetch` + `merge`, konflikt =
dvě větve změnily tutéž pasáž a Git odmítá hádat.

### Týmové vzory
- **Branch per změna** (`feature/inventura-webu`), krátký život větve (dny, ne týdny).
- **PR jako brána**: review druhým člověkem, u automatizace čtyři oči na produkční
  změně; checklist z D1 (identifikátory, error handling, `-WhatIf`).
- **Merge vs. rebase vs. squash** — pro malý tým: merge (historie se nefalšuje),
  squash pro úklid „WIP" commitů před mergem; rebase až s jistotou (přepisuje
  historii — nikdy na sdílené větvi).
- **Ochrana `main`**: branch policy (Azure DevOps) / branch protection (GitHub) —
  povinný PR, min. 1 reviewer, build musí projít.

### Hosting v praxi veřejné správy
Rekapitulace doporučení z [`explainer-git-hosting.md`](../../day-1/vscode-copilot-env/explainer-git-hosting.md)
(Azure DevOps default — Entra identita, EU region) + co základní běh nepokryl:
struktura organizace vs. projektů, licence (Basic vs. Stakeholder), service hooks,
napojení Boards na repo (commit `#123` zavře work item).

## Laby

- **Lab A — Branch, PR a konflikt ve dvojici** ([`lab-branch-pr-conflict.md`](lab-branch-pr-conflict.md)):
  dva lidé, jeden repozitář, obě větve mění stejný skript → PR, review, konflikt,
  vyřešení ve VS Code, merge.
- **Lab B — Vlastní Azure DevOps organizace** ([`lab-azdo-org-setup.md`](lab-azdo-org-setup.md)):
  založení organizace s Entra ID, EU region, projekt + repo, branch policy na `main`,
  import kurzovního repa.

## Klíčové rozlišení
- **Commit jako snímek vs. „uložení souboru"** — Git verzuje stav celého projektu;
  z toho plyne, proč jsou malé commity levné a velké nebezpečné.
- **`fetch` vs. `pull`** — podívat se, co je venku, vs. rovnou si to vzít; ve
  scénářích „nevím, co tam kolegové dali" je `fetch` + prohlídka bezpečnější.
- **Merge (nový commit, historie zachována) vs. rebase (přepis historie)** — rebase
  nikdy na větvi, kterou už viděl někdo jiný.
- **Branch policy vs. dobré úmysly** — pravidlo vynucené serverem přežije spěch
  i nováčka; dobré úmysly ne.

## Zdroje
- [Pro Git — kniha zdarma, česky](https://git-scm.com/book/cs/v2)
- [Azure DevOps — create an organization](https://learn.microsoft.com/en-us/azure/devops/organizations/accounts/create-organization)
- [Azure Repos — branch policies](https://learn.microsoft.com/en-us/azure/devops/repos/git/branch-policies)
- [Learn Git Branching (interaktivní vizualizace)](https://learngitbranching.js.org/?locale=cs_CZ)

## Stav produktu / delta
> [!WARNING]
> Ověřit k datu běhu — Azure DevOps free tier (5× Basic), proces zakládání organizace
> a dostupnost EU regionů se mění; Lab B projít na čistém účtu před během.
