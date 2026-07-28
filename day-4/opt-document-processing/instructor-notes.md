# Instructor notes — Document processing

## Timing

- Největší blok dne: 40 min výklad + konfigurace, 2× 10 min demo, 40 min lab, 10 min nálezy. Chránit před skluzem dopoledních bloků — radši zkrátit výklad než dema a lab.

## Před během — checklist

- [ ] PAYG billing aktivní; autofill + image tagging **scope zahrnuje studentské weby** (Selected sites — přidat všech 20).
- [ ] Sada vzorků: 3 dokumenty CZ (smlouva, objednávka, zápis — s jasnou „protistranou") + 3 obrázky (jeden schválně víceznačný, ať tagging ukáže limity), distribuce přes odkaz.
- [ ] Demo knihovna připravená, ale autofill sloupec zakládat AŽ živě — to je celé demo.
- [ ] Budget alert zkontrolovaný — 20 studentů × ~10 transakcí je drobné, ale viditelné.
- [ ] Structured/freeform: pokud se má ukázat víc než slide, ověřit **Power Platform environment + kreditovou cestu** (AI Builder kredity nemáme — jde to přes Copilot Credits dual-mode? viz `comparison-models.md`). Bez toho jen výklad nad maticí, demo nechat na prebuilt/unstructured (M365 PAYG).

## Dema

1. **Autofill**: sloupec „Protistrana" + prompt sloupce; ukázat dobrý vs. líný prompt (váže na ranní prompting blok). Ukázat přepis hodnoty člověkem.
2. **Image tagging**: zapnout na knihovně, nahrát obrázky, ukázat tagy + kde se spravují (managed metadata). Zdůraznit: účtuje se per knihovna se zapnutým taggingem.

## Tripwires

- Neříkat Syntex ani SharePoint Premium (glosář); URL v docs „syntex" — upozornit jednou.
- **Autofill překryv s Copilot in SharePoint** (preview, přes Copilot licenci bez PAYG) — zmínit, náš tenant jede PAYG cestou.
- Studenti rádi nahrají 50 obrázků „na zkoušku" — vzorek je 3+3, říct proč (PAYG).
- eSignature z rodiny nevykládat — má vlastní blok zítra (D3).

## Vazby

- Zpět: prompt anatomie (ranní blok) přímo v promptu autofill sloupce; PAYG modely (D4 licensing).
- Dopředu: náklady/observabilita → D5 `opt-monitoring`; eSignature a vytěžování faktur (prebuilt vs. Azure AI Document Intelligence) jsou mimo tento běh (plný kurz GOC224).
