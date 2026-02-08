# AutoDiario — Changelog

Gestionale manutenzioni e spese auto.
Formato basato su [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), versioni con [Semantic Versioning](https://semver.org/).

---

## [2.4.0] — 2026-02-08

### Gomme di serie (OEM)

- **Treno gomme originali** — Aggiunto supporto per le gomme di serie montate in fabbrica. Vengono gestite in modo implicito: nessun record di montaggio nel registro, ma il conteggio km e lo smaltimento sono tracciati regolarmente. Le originali partono sempre da km 0, anche per auto acquistate usate.
- **C4 — "Di serie → Pirelli P7"** — Treno unico con 8 units: 4 originali (smaltite gradualmente a km 60.000 e 79.000) + 4 Pirelli P7 (sostitutive, smaltite a km 115.000). Seconda ipotesi applicata: 2 P7 a km 60.000 (€160) rimpiazzano 2 originali, 2 P7 anteriori a km 79.000 (€190) rimpiazzano le ultime 2.
- **C5 Aircross — "Di serie 225/55R18"** — Gomme OEM tracciate da km 0 a km 55.160 (smaltite il 28/11/2024, sostituite con Michelin Alpine 7).
- **Record GA di fabbrica** — Record invisibile nel registro (filtrato da `curRecs`) ma necessario per il sync interno. Costo €0, km 0.

### Correzioni sync engine (`syncAllTiresFromRecords`)

- **Preservazione mount impliciti** — Il sync ora preserva i mount events che non hanno un record GM corrispondente (gomme di fabbrica), anziché sovrascriverli. Funziona per qualsiasi km di partenza (km 0 per auto nuove, qualsiasi km per auto usate).
- **Conteggio montaggi** — Esclusi i mount impliciti (senza `recordId`) dal contatore "MONTAGGI" visualizzato nella scheda gomme.

### Correzioni eliminazione record gomme

- **Bug `cDel` — eliminazione GA secondario bloccata** — Quando si aggiungevano gomme a un treno esistente e poi si voleva eliminare l'acquisto secondario, il codice bloccava l'operazione contando tutti i record GM/GS del treno (anche quelli preesistenti). Ora distingue tra GA primario e GA secondario: il secondario verifica solo i record specifici delle units aggiunte.
- **Bug `dRecGA` — units orfane** — Quando si elimina un GA secondario, ora vengono rimosse anche le units associate (matching per `addedKm`), ripristinando il treno allo stato originale.

### Dati JSON

- **Ridondanze rimosse** — Eliminati 4 record ODS duplicati dai record gomme generati:
  - C5: "Gomme Michelin alpine 7" (€880), "Gomme Hankook Ventus Prime4" (€680), "cambio gomme" (€50)
  - C4: "Gomme estive Michelin Energy saver + 1 paio" (€240)
- **Energy Saver+ (t4)** — Aggiunto record smaltimento 2 gomme anteriori usurate (km 248.769). Corretto evento `unmount` mancante per le units smaltite u15/u16.
- **Date mancanti dal ODS** — Integrate 4 date aggiunte nel file ODS: Tagliando km 60.000 (16/03/2010), Gomme Pirelli P7 km 60.000 (17/04/2010), Batteria km 65.000 (18/05/2010), Lampadina H7 km 75.000 (19/06/2010).

---

## [2.3.0] — 2026-02-07

### Gomme — integrazione bidirezionale con registro

- **Record automatici** — Montaggio/smontaggio/acquisto gomme generano automaticamente voci nel registro con descrizione, km, costo e rivenditore precompilati.
- **Badge 🛞** — Le voci del registro collegate a un treno gomme mostrano un badge cliccabile per navigare al tab Gomme.
- **Filtro registro da gomme** — Pulsante "📋 Registro" in ogni treno gomme filtra il registro mostrando solo le voci collegate.
- **Smaltimento gomme** — Pulsante "⚠️ Smaltisci gomma" con selezione posizione, motivo e note. Crea record GD e aggiorna status delle units.
- **Ripristino gomme** — Pulsante "♻️ Ripristina" per annullare uno smaltimento, rimuovendo il record GD corrispondente.

### Salvataggio dati — tre modalità

- **Manuale**: pulsante "💾 Salva" nell'header, diventa arancione con pallino quando ci sono modifiche non salvate.
- **Automatico**: ogni X minuti (configurabile, default 30).
- **All'uscita**: `save()` su localStorage + backup JSON se autosave attivo e sono passati almeno 5 minuti.

### Calcolo km per singola gomma (units)

- Unità originali: eventi dal primo montaggio fino a disposeKm.
- Unità sostitutive: eventi dal loro `addedKm` in poi.
- Unità smaltite: km calcolati solo fino a `disposeKm`.
- Il treno mostra il massimo km tra le units attive.

---

## [2.2.0] — 2026-02-06

### Analisi — costo veicolo integrato

- **Checkbox "Includi costo veicolo nei calcoli"** — Aggiunge "Veicolo/km" ai costi/km, riga "🚗 Veicolo" + "COSTO REALE" nella tabella annuale, fetta "Veicolo" nel grafico a torta.
- **Costo netto**: acquisto − vendita.
- **Grafico km per treno gomme** — Aggiunto nelle statistiche gomme.

### PDF — generazione diretta

- **jsPDF dinamico** — Caricato al momento della generazione (non al caricamento pagina).
- **Fallback locale** — Se CDN non raggiungibile (`file://`), apre finestra con anteprima e pulsante "Stampa / Salva PDF".
- **Costo veicolo incluso nel PDF**.

### Anagrafiche

- **Protezione cancellazione** — Se ci sono voci nel registro, il tipo non può essere eliminato.
- **Codice non modificabile** — Una volta creato, il codice del tipo è immutabile.
- **Tipi custom nel form** — I tipi creati dall'utente appaiono nel selettore del registro.
- **Gruppi** — 5 gruppi (Manutenzione, Tasse, Gomme, Accessori, Altro) con badge colorato.
- **Controllo codice duplicato** — Verifica unicità prima del salvataggio.

---

## [2.1.0] — 2026-02-05

### Esportazioni — formato JSON

- Backup/ripristino: JSON completo.
- Registro veicolo: JSON con anagrafica + record + gomme.
- Rivenditori: JSON.
- Gomme: JSON con events.
- Rimossi tutti i vecchi export CSV.

### Scheda veicolo PDF

- Funzione `genPDF()`: anagrafica, riepilogo generale (km, spesa totale, costo/km), ripartizione spese per categoria, acquisto/vendita, riepilogo annuale, tabella gomme, registro completo.

### Analisi e riepilogo annuale

- 5 categorie di spesa (Ricambi, Manutenzione, Gomme, Tasse, Accessori).
- Dashboard, grafici a barre, trend annuali, doughnut ripartizione.
- Costo gomme/km.
- Riga TOTALE nel riepilogo.
- Pie chart ripartizione spese.

### Ricerca registro

- Input di ricerca non perde più il focus durante la digitazione. `rRecs()` delega la tabella a `rRecsTable()` che aggiorna solo il wrapper senza ricostruire la toolbar.

---

## [2.0.0] — 2026-02-04

### Gestione gomme completa

- **Treni gomme** — CRUD completo: marca, modello, misura, stagione, DOT, costo, rivenditore.
- **Units per gomma** — Tracciamento individuale per posizione (ASx, ADx, PSx, PDx + scorte).
- **Montaggio/smontaggio** — Con data, km, costo e rivenditore. Crea eventi nel treno.
- **Stagioni** — Estive ☀️, Invernali ❄️, 4 Stagioni 🌤️.
- **Status automatico** — active/stored/disposed calcolato dagli eventi.
- **Statistiche** — Km percorsi, costo totale, costo/km, numero montaggi per treno.

### Registro

- 9 tipi record: R (Ricambio), M (Manutenzione), T (Tassa), A (Accessorio), GA (Acquisto gomme), GM (Montaggio gomme), GS (Smontaggio gomme), GD (Smaltimento gomme), GL (Lavaggio gomme).
- Ordinamento per colonna, filtri per tipo/anno, ricerca testuale.
- Modifica inline con form modale.

### Multi-veicolo

- Supporto veicoli multipli con anagrafica completa (marca, modello, targa, anno, motore, VIN, carburante, note, acquisto/vendita).
- Passaggi di proprietà tracciati.
- Switcher rapido nell'header.

### Dati

- Caricamento automatico da `autodiario_data.json` via fetch.
- Fallback a localStorage se fetch fallisce.
- Welcome screen con upload manuale se nessun dato presente.
- Backup/ripristino JSON completo.

---

## [1.0.0] — 2026-02-03

### Release iniziale

- Interfaccia single-page HTML (zero dipendenze server).
- Dark theme con design responsive.
- Registro manutenzioni e spese.
- Dashboard con statistiche base.
- Import dati da file ODS.
- Salvataggio su localStorage.
