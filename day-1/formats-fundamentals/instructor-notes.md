# Instructor notes — JSON, YAML a PowerShell od nuly

## Timing

- 60 min výklad + 60 min Lab 1. Výklad dělit: JSON 15 min, YAML 5 min (opravdu jen
  ukázat), PowerShell 30 min, Copilot workflow 10 min.
- Lab 1 je první úspěch dne — musí ho dokončit **každý**. Radši seškrtat krok 6 než
  nechat půlku skupiny bez funkčního skriptu.

## Go/no-go — KLÍČOVÉ

- M365 Copilot Chat free je dostupný na kurzovních účtech `cloudedu.cz` a ukazuje
  ikonu štítu (enterprise data protection) — otestovat na studentském účtu (ne admin)
  den před během. Bez toho padá lab i blok `vscode-copilot-env`.
- Připravit `sites.json` (~15 fiktivních webů, ať filtrování dává smysl) a záložní
  `first-script-sample.ps1` do sdílené složky.

## Tripwires

- Publikum jsou začátečníci — na `Get-Member` a `Get-Help -Examples` vracet pozornost
  opakovaně; to jsou dvě věci, které si musí odnést, i kdyby zapomněli syntax.
- Nejčastější JSON chyba: čárka za posledním prvkem pole nebo chybějící uvozovky —
  nechat editor chybu ukázat, je to učební moment, ne zdržení.
- U kroku 4 hlídat, aby účastníci skript opravdu četli, ne jen odklikli — namátkově se
  ptát „co dělá řádek X?" už během labu.
- YAML nerozvíjet: jakmile padne otázka na Ansible/Kubernetes, odkázat na přestávku.
- Nezmiňovat pipeline triky (kalkulované vlastnosti, `ForEach-Object -Parallel`) —
  ohromí, ale odradí.

## Vazby

- Dopředu: workflow „prompt s kritérii → číst → testovat" formalizuje
  [`../vscode-copilot-env/`](../vscode-copilot-env/); `param()`/`try/catch` vzor se
  vrací ve všech labech; JSON čtení v [`../../day-3/graph-fundamentals/`](../../day-3/graph-fundamentals/).
- Zpět: JSON jako formát odpovědí API ukázalo demo v [`../api-landscape/`](../api-landscape/).
