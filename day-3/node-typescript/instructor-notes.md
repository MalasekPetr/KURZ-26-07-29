# Instructor notes — Node.js & TypeScript

## Timing

- 30 min výklad + 20 min demo. Záměrně bez labu — publikum má za sebou dva dny
  PowerShellu a cíl je „vědět, že cesta existuje", ne druhý jazyk za odpoledne.
  Při skluzu dne je tohle první blok ke zkrácení (výklad 15 min, demo vypustit).

## Go/no-go

- Demo skript funguje na instruktorském stroji: Node LTS + `npm install` projde ze
  sítě učebny (jinak `node_modules` přinést s sebou), certifikátová identita z D2
  demo app registrace platná.

## Tripwires

- Nenechat blok přerůst v kurz TypeScriptu — žádná syntaxe na flipchartu; jediný kód,
  který účastníci vidí, je hotový skript z explaineru.
- Otázka „máme se učit TS?" — odpověď podle publika: pro adminy/automatizéry ne,
  PowerShell pokryje vše z kurzu; TS je relevantní, až/pokud vznikne vývojářský tým
  nebo potřeba SPFx.
- Zdůraznit kontinuitu, ne novost: stejná app registrace, stejný cert, stejné API —
  to je pointa bloku (a důkaz věty „moduly umírají, REST zůstává" z D1).

## Vazby

- Dopředu: CI/CD s node image zmiňuje [`../azure-orientation/`](../azure-orientation/)
  (kontejnery); SPFx a plný GOC223 jako navazující běh.
- Zpět: auth matice z [`../../day-2/powershell-deep-dive/`](../../day-2/powershell-deep-dive/),
  certifikát z [`../../day-2/certificates-and-keys/`](../../day-2/certificates-and-keys/),
  „wrapper vs REST" z [`../../day-1/api-landscape/`](../../day-1/api-landscape/) a
  [`../../day-2/automation-strategy/`](../../day-2/automation-strategy/).
