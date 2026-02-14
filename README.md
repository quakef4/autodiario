# AutoDiario 🚗

Gestionale manutenzioni e spese auto — applicazione single-page HTML senza dipendenze server.

## Caratteristiche

- **Multi-veicolo** — Gestione di più veicoli con anagrafica completa
- **Registro manutenzioni** — Ricambi, manutenzione, tasse, accessori con filtri e ricerca
- **Gestione gomme** — Treni gomme con tracciamento individuale per posizione, stagioni, montaggi/smontaggi, smaltimenti, km per singola gomma
- **Dettaglio lotti gomme** — Breakdown per lotto nei treni multi-lotto (sostituzione parziale), con sub-splitting per km unitari e indicatore di smaltimento
- **Gomme di serie** — Gestione implicita delle gomme OEM con conteggio km e smaltimento, escluse dalle statistiche finanziarie
- **Analisi e statistiche** — Dashboard, grafici, trend annuali, ripartizione spese, costo/km
- **Esportazione PDF** — Scheda veicolo completa in formato PDF
- **Backup JSON** — Salvataggio manuale, automatico e all'uscita

## Installazione

1. Scarica `AutoDiario_v2.html` e `autodiario_data.json`
2. Mettili nella stessa cartella
3. Apri `AutoDiario_v2.html` nel browser

L'app carica automaticamente i dati dal file JSON. Se non trova il file, mostra la schermata di benvenuto con opzione di upload manuale.

## Struttura file

```
AutoDiario_v2.html      — Applicazione completa (HTML + CSS + JS)
autodiario_data.json    — Dati veicoli, registri e gomme
CHANGELOG.md            — Cronologia modifiche
README.md               — Questo file
LICENSE                  — Licenza GPL v2
.gitignore              — File esclusi dal versioning
```

## Dati

I dati sono salvati in formato JSON con questa struttura:

```json
{
  "vehicles": [
    {
      "id": "...",
      "name": "...",
      "brand": "...",
      "records": [...],
      "tires": [...]
    }
  ],
  "types": [...],
  "dealers": [...]
}
```

### Backup

- **Manuale**: pulsante 💾 nell'header
- **Automatico**: ogni X minuti (configurabile)
- **All'uscita**: salvataggio su localStorage + backup JSON

## Tecnologie

- HTML5 / CSS3 / JavaScript ES6+
- Chart.js (CDN) per grafici
- jsPDF (CDN) per generazione PDF
- Zero backend — tutto gira nel browser
- localStorage per persistenza locale

## Licenza

GPL v2 — vedi [LICENSE](LICENSE).

## Changelog

Vedi [CHANGELOG.md](CHANGELOG.md) per la cronologia completa delle modifiche.
