# BACS (Building Automation and Control System)
# Automazione per risparmio energetico di dispositivi domotici in Home Assistant

Questo blueprint è progettato per semplificare l'integrazione di termostati con sensori finestra, in conformità alla normativa **UNI EN 15232-1:2017** sui sistemi di automazione degli edifici (BACS – Building Automation and Control Systems).

## Contenuto

La directory `blueprints/automation/bacsautomations/` contiene un unico blueprint:

**gestione_termostato_finestra.yaml**

Un'automazione unica che gestisce l'intero ciclo di vita dell'interazione finestra/termostato tramite tre rami logici interni:

1. **Apertura finestra** – Quando la finestra si apre e resta aperta per 30 secondi, salva lo stato attuale del termostato (qualunque esso sia, incluso `off`) e lo spegne.
2. **Chiusura finestra** – Quando la finestra si chiude e resta chiusa per 30 secondi, ripristina lo stato salvato, ma solo se nel frattempo l'utente non è già intervenuto manualmente sul termostato.
3. **Blocco riaccensione** – Se, a finestra aperta da più di 30 secondi, qualcuno tenta di riaccendere il termostato, questo viene rispento immediatamente.

Rispetto alle versioni precedenti (basate su tre blueprint distinti), l'automazione unica introduce due comportamenti aggiuntivi pensati per non ostacolare l'utente:

- **Finestra di tolleranza in apertura**: nei primi 30 secondi dopo l'apertura della finestra, l'utente può ancora cambiare liberamente la modalità del termostato. Allo scadere dei 30 secondi, è la modalità scelta dall'utente (non quella precedente all'apertura) a venire salvata prima dello spegnimento.
- **Rispetto delle scelte manuali in chiusura**: se durante i 30 secondi di attesa dopo la chiusura della finestra l'utente imposta manualmente una modalità sul termostato, questa non viene sovrascritta dal ripristino automatico allo scadere del timer.

## Installazione

1. Copia la cartella `bacsautomations` in: config/blueprints/automation/

2. Riavvia Home Assistant oppure ricarica i blueprint da:

   > Impostazioni → Automazioni → Blueprint → Menu (in alto a destra) → Ricarica blueprint

3. Crea un **helper input_select** per ogni termostato da gestire, da:

   > Impostazioni → Dispositivi e servizi → Helper → + Crea Helper → Selezione

   - Aggiungi le opzioni corrispondenti ai possibili stati HVAC del termostato (`off`, `heat`, `cool`, `auto`).
   - Dai un nome significativo (es. `termostato_soggiorno_stato`).

4. Crea una nuova automazione utilizzando il blueprint:

   - Vai su **Impostazioni → Automazioni → + Crea automazione → Usa blueprint**.
   - Seleziona **gestione_termostato_finestra** in `bacsautomations`.
   - Compila i campi richiesti:
     - Sensore finestra (`binary_sensor`)
     - Termostato (`climate`)
     - Input Select stato salvato (`input_select` creato al punto 3)
   - Salva l'automazione.

5. Ripeti il punto 4 per ogni coppia sensore finestra / termostato da gestire, utilizzando un `input_select` dedicato per ciascuna coppia.

## Riferimenti normativi

Questo sistema contribuisce all'efficienza energetica degli edifici secondo:

**UNI EN 15232-1:2017 – Prestazione energetica degli edifici – Influenza dell'automazione, del controllo e della gestione tecnica dell'edificio (BACS)**.

## Contributi

Progetto mantenuto da **[Flavio](https://github.com/odoricof)**.
Segnalazioni e miglioramenti sono benvenuti!
