# Lab · Postavte základního copilota

> Modul: copilot-studio · Odhad: 50 min · Režim: živý tenant (go/no-go instruktora); simulace fallback

## Cíl

Postavit v Copilot Studiu agenta nad vlastním SharePoint webem a protáhnout ho evaluačním plánem z dopoledne.

## Předpoklady

- Přístup do copilotstudio.microsoft.com (vyhlásí instruktor — PAYG/trial).
- Návrh agenta z dopoledního labu (instrukce, knowledge, 5 testů).

> [!NOTE] Návaznost na D4
> Kdo v D4 stavěl **HR Asistenta** ([`../../day-4/copilot-agents/scenario-hr-agent.md`](../../day-4/copilot-agents/scenario-hr-agent.md)): tady ho dotáhni na **analytické dotazy** nad listem `Zaměstnanci` („komu propadá certifikát do 30 dnů", „kdo nepodepsal") — to je přesně to, co Agent Builder ani SharePoint agent neuměly. Copilot Studio je jediná cesta na agregační dotazy (až 10 listů).

## Kroky

1. Založ agenta (New agent) — jméno a **popis piš pečlivě**: generative orchestration podle něj routuje.
2. Vlož instrukce z dopoledního návrhu (part A, krok 2).
3. Přidej **knowledge: SharePoint** — URL vlastního webu. Ověř v nastavení, že jede plná integrace (ne upload).
4. V testovacím panelu spusť **5 testů z evaluačního plánu** vč. negativního testu a testu hranice práv (požádej souseda, ať položí dotaz na tvůj web, na který nemá práva — čekáme „no response").
5. Zapiš skóre (x/5) a jednu úpravu instrukcí/popisu, která nejhorší test zlepšila (iterace = D2 lekce).
6. **Nepublikovat do kanálů** — publikace je instruktorské demo (org flow: Requests → admin, viděli jste v D4).

## Ověření

- [ ] Agent odpovídá z obsahu vlastního webu (ne obecně).
- [ ] Negativní test a test práv dopadly podle očekávání („no response" u cizího webu).
- [ ] Zapsané skóre + jedna provedená iterace se změřeným efektem.
- [ ] Agent nepublikovaný (governance!).

## Fallback

- Copilot Studio nedostupný pro studenty: instruktor staví živě podle návrhu vybraného studenta; třída píše testy a predikce, pak se ověří. Ostatní návrhy: studenti si doma postaví v trialu.

## PAYG poznámka

Každý testovací dotaz = Copilot Credits. 5 promyšlených testů + 1 iterace, ne brute-force ladění.
