# dsh-restart

Een plug-in om het volledige DeepSeek Harness-proces opnieuw op te starten. Hiermee worden plug-ins en configuraties opnieuw geladen (zoals de Cordis-combinatie van het profiel, instellingen enzovoort).

De plug-in bestaat uit twee onderdelen: een host- en een clientgedeelte. Zodra beide in de bundle van een profiel zijn opgenomen, kan de plug-in worden gebruikt.

## Functies

- Modeltool `restart_harness`: Hiermee kan de agent rechtstreeks een herstart inplannen (met optionele parameter `delayMs`).
- Slash-opdracht `/restart`: Hiermee kun je de herstart handmatig vanuit de gebruikersinterface uitvoeren.
- Configuratiekaart (Instellingen → Plug-ins → Plug-inconfiguratie → 'DSH Restart'): Hiermee kun je de volgende instellingen visueel bewerken. Wijzigingen worden direct opgeslagen in `settings.yaml`:
  - `legacyRestart` — De oude herstartmethode via PowerShell/WMI/taskkill (standaard uitgeschakeld; standaard wordt de native Node.js-methode gebruikt).
  - `continuePrompt` — De prompt die na een herstart automatisch aan de agent wordt doorgegeven om verder te gaan.
  - `watchdogEnabled` — Start DSH automatisch opnieuw op bij een crash of afsluiting (standaard uitgeschakeld; gebruik deze optie voorzichtig).
  - `watchdogCooldownMs` / `watchdogPollMs` — De afkoelperiode en het interval waarmee de watchdog controleert.
- Knop 'Nu opnieuw opstarten': Hiermee kun je DSH direct opnieuw opstarten via een endpoint op dezelfde host.
  Om veiligheidsredenen accepteert dit endpoint uitsluitend POST-verzoeken van dezelfde host via een loopbackadres (`127.0.0.1` / `localhost`). Bij toegang via een reverse proxy of op afstand wordt de aanvraag geweigerd met foutcode 403 (Forbidden).

## Installatie

1. Voeg het pakket toe aan de profielafhankelijkheden en neem het op in de bundle:

jsonc

```jsonc
// profiles/<profile>/package.json
{
  "dependencies": { "dsh-restart": "..." },
  "dsh": { "profile": { "bundles": ["...", "dsh-restart"] } }
}
```

2. Start DSH opnieuw op (via `/restart` of `restart_harness`) en vernieuw vervolgens de pagina. De configuratiekaart wordt nu zichtbaar.

## Bouwen (Build)

bash

```bash
pnpm install
node scripts/link-dsh-workspace.mjs --source <path-to-deepseek-harness>
pnpm run build
```

- Het hostgedeelte wordt met `tsc` gecompileerd naar `lib/index.js`. De afhankelijkheden van `@deepseek-ai/*` blijven externe afhankelijkheden.
- Het clientgedeelte wordt met `tsdown` gebundeld tot één enkel bestand: `lib/client.js`.
