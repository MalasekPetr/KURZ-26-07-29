# Lab · Nastavení ukázkové knihovny

> Modul: document-processing · Odhad: 40 min · Režim: živý tenant (vlastní web; PAYG — malý vzorek!)

## Cíl

Zopakovat instruktorská dema na vlastní knihovně: autofill sloupec + image tagging, a změřit, co to udělalo s metadaty.

## Předpoklady

- Vlastní web (Owner), PAYG služby zapnuté pro studentské weby (instruktor).
- Sada vzorků od instruktora: 3 dokumenty (smlouva/objednávka/zápis) + 3 obrázky.

## Kroky

1. Založ knihovnu `Vzorky` a nahraj 3 dokumenty + 3 obrázky.
2. **Autofill:** přidej sloupec (např. „Protistrana" nebo „Shrnutí"), v nastavení sloupce zapni autofill a napiš prompt sloupce — co přesně má z dokumentu vytáhnout (využij anatomii promptu z rána: cíl + očekávání!).
3. Sleduj, jak se sloupec plní; u jednoho dokumentu hodnotu zpochybni a přepiš — všimni si, že člověk má poslední slovo.
4. **Image tagging:** na knihovně zapni enhanced image tagging; zkontroluj tagy u 3 obrázků. Který tag je špatně/nepřesně? Zapiš.
5. Do MD poznámky v knihovně: kolik transakcí lab zhruba stál (počty × ceník z README) a jak bys spotřebu doložil (Azure Cost Management, zpoždění 24 h).

## Ověření

- [ ] Autofill sloupec plní hodnoty u všech 3 dokumentů; prompt sloupce má cíl i očekávání.
- [ ] Image tagging zapnutý jen na téhle knihovně (ne na celém webu bez rozmyslu — spotřeba!).
- [ ] Poznámka obsahuje odhad nákladů a místo, kde se ověří.

## Fallback

- Autofill nedostupný na studentských webech (scope/preview): studenti píšou prompt sloupce a předají instruktorovi, který je nasadí na demo knihovně; kroky 4–5 beze změny.
- Image tagging nefunguje: analýza tagů na instruktorově demo knihovně (read-only).
