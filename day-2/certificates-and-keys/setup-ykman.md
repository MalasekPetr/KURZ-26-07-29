# Návod (volitelný) · Instalace ykman pro vlastní YubiKey

Pro sledování YubiKey dema nic instalovat nemusíte. Tento návod je pro ty, kdo si
chtějí postup z [`demo-yubikey.md`](demo-yubikey.md) vyzkoušet na **vlastním**
YubiKey 5 — během kurzu nebo doma.

## Instalace (Windows, winget)

```powershell
winget install Yubico.YubiKeyManagerCLI            # ykman — CLI pro správu klíče
winget install Yubico.YubiKeySmartCardMinidriver   # propíše cert z klíče do Cert:\CurrentUser\My
```

Minidriver je pro krok „připojení s dotykem" nutný — bez něj `Connect-PnPOnline
-Thumbprint` certifikát na klíči nenajde. Windows si ho někdy stáhne sám přes
Windows Update při prvním vložení klíče, ale instalace wingetem je jistota.

> [!NOTE]
> Pozor na podobně pojmenovaný balíček `Yubico.YubikeyManager` — to je starší,
> už nevyvíjená GUI aplikace. Pro tento návod potřebujete **`Yubico.YubiKeyManagerCLI`**.

### Alternativy bez wingetu

- MSI instalátor: [developers.yubico.com/yubikey-manager/Releases](https://developers.yubico.com/yubikey-manager/Releases/)
- Přes Python: `pip install yubikey-manager`
- Linux/macOS: `brew install ykman` / balíček `yubikey-manager` v distribuci

## Ověření (nový terminál, klíč v USB)

```powershell
ykman --version
ykman list        # vypíše připojený YubiKey (model, sériové číslo)
ykman piv info    # PIV aplet: sloty, verze, zbývající PIN pokusy
```

## Než začnete: změňte výchozí PIV kódy

Z výroby má PIV aplet PIN `123456`, PUK `12345678` a výchozí management key —
první věc, kterou útočník zkusí. Před jakýmkoli generováním klíčů:

```text
ykman piv access change-pin
ykman piv access change-puk
ykman piv access change-management-key --generate --protect
```

`--protect` uloží management key na klíč chráněný PINem — nemusíte si pamatovat
48 hex znaků.

> [!WARNING]
> PIV aplet se po 3 špatných PIN + 3 špatných PUK pokusech zablokuje a řeší se
> resetem PIV apletu (`ykman piv reset` — smaže klíče a certifikáty v PIV slotech,
> ostatních funkcí YubiKey se nedotkne). Na klíči, který zároveň používáte jako
> MFA do produkce, experimentujte s rozmyslem — PIV a FIDO2 jsou oddělené
> „přihrádky", ale reset PIV smaže vše, co jste si v PIV vytvořili.

## Co dál

Pokračujte kroky 2–6 v [`demo-yubikey.md`](demo-yubikey.md) — jen proti vlastní
testovací app registraci (postup registrace: Lab 2,
[`../automation-strategy/lab-app-registration.md`](../automation-strategy/lab-app-registration.md)).

## Zdroje (Yubico)

- [YubiKey Manager CLI — dokumentace](https://developers.yubico.com/yubikey-manager/)
- [PIV — koncepty a sloty](https://developers.yubico.com/PIV/Introduction/YubiKey_and_PIV.html)
- [Smart Card Minidriver](https://www.yubico.com/support/download/smart-card-drivers-tools/)
