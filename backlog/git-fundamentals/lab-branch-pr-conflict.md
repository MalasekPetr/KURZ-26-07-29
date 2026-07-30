# Lab A · Branch, pull request a konflikt ve dvojici

> Odhad: 60 min · Režim: dvojice, sdílený repozitář (Azure DevOps nebo GitHub)

## Cíl

Projít celý týmový cyklus na vlastní kůži: větev → commity → push → pull request →
review → **konflikt → vyřešení** → merge. Konflikt je v tomto labu záměr, ne nehoda —
cíl je, aby přestal být strašákem.

## Předpoklady

- Dvojice (řidič A + řidič B, v polovině prohodit role).
- Sdílený repozitář, do kterého mají oba push práva (z Labu B, nebo připraví lektor).
- V repu skript `report.ps1` (stačí kostra s `param()` a třemi řádky těla).

## Kroky

1. **Oba**: `git clone`, pak každý vlastní větev:
   A: `git switch -c feature/a-pridava-filtr` · B: `git switch -c feature/b-meni-vystup`.
2. **Oba upraví tentýž řádek** `report.ps1` (záměrně — např. oba změní výchozí hodnotu
   téhož parametru na jinou hodnotu) + každý přidá jednu vlastní nekonfliktní změnu.
   Commit s popisnou zprávou (PROČ), push větve.
3. **A otevře PR** své větve do `main`; **B udělá review** (aspoň jeden komentář
   k věci — checklist z D1), A zapracuje, B schválí, **merge**. Zatím žádný konflikt.
4. **B otevře PR** — a teď to přijde: jeho větev vznikla před mergem A, PR hlásí
   **konflikt**. Přečíst si, co přesně hosting říká (which files conflict).
5. **B řeší konflikt lokálně**:

   ```powershell
   git switch feature/b-meni-vystup
   git pull origin main          # merge main do mé větve → konflikt v report.ps1
   ```

   VS Code ukáže obě verze (Current/Incoming); **rozhodnutí je na člověku** — vybrat,
   zkombinovat, výsledek uložit, otestovat spuštěním, pak:

   ```powershell
   git add report.ps1
   git commit                    # merge commit
   git push
   ```

6. PR se automaticky aktualizuje, konflikt zmizel → review → merge.
7. **Prohodit role** a zopakovat kroky 2–6 (druhé kolo je znatelně rychlejší —
   přesně to je cíl).

## Ověření

- [ ] Oba PR mergnuté, `main` obsahuje změny obou a funguje (skript jde spustit).
- [ ] Konflikt vyřešen vědomým rozhodnutím (účastník umí říct, proč vybral kterou
      verzi), ne mechanickým „Accept Current".
- [ ] Historie `main` čitelná: malé commity s PROČ zprávami, žádný „fix fix fix2".
- [ ] Každý absolvoval obě role (autor PR i reviewer).

## Fallback

Bez funkčního sdíleného repa lze lab odjet lokálně: dvě větve v jednom klonu, merge
mezi nimi — chybí PR/review vrstva, konflikt a jeho řešení jsou identické.
