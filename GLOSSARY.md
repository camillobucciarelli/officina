# Officina

La pipeline condivisa da copista, postino e custode: gli stessi stage, con le stesse regole, per ogni app.

## Language

### Pipeline della PR

**Gate PR**:
L'insieme dei controlli che una PR deve superare prima del merge.
_Avoid_: CI, checks

**Esito PR**:
Il solo check richiesto dalla branch protection: è verde solo se ogni controllo del Gate PR è verde o saltato per regola.
_Avoid_: required checks, status

**Controllo rapido**:
Un controllo del Gate PR che non compila l'app: formato, analisi, codice generato, test, scansione di dipendenze e segreti.
_Avoid_: lint, check

**Build nativa**:
La compilazione dell'app per una piattaforma, senza firma. Nel Gate PR esiste solo se la PR cambia codice nativo.
_Avoid_: build di verifica, smoke build

**Golden**:
Un'immagine di riferimento dell'interfaccia, registrata e confrontata sullo stesso tipo di macchina macOS.
_Avoid_: screenshot test, snapshot

### Rilascio

**Release**:
La pubblicazione di una versione dell'app, avviata a mano da una persona con la scelta del Canale.
_Avoid_: deploy, ship

**Canale**:
Il tipo di una Release: `beta` o `release`. Gli store ricevono entrambi allo stesso modo; il Canale cambia solo il tag, la pre-release su GitHub e la pagina del sito che la mostra.
_Avoid_: track, flavor

**Numero di build**:
Il contatore unico di un'app, comune a tutte le versioni e a tutti i Canali. Non scende mai e non riparte mai.
_Avoid_: build counter, versionCode, run number

**Firma**:
Il passo che usa le chiavi dell'app. Avviene solo sulla macchina self-hosted e mai con codice di una PR.
_Avoid_: signing, codesign

### Sito

**Pagina senza versione**:
Una pagina del sito che non nomina una versione dell'app; esce al merge.
_Avoid_: static page

**Pagina download**:
Una pagina del sito che nomina una versione e i suoi file; esce solo da una Release. La stabile e la beta sono pagine separate.
_Avoid_: download page, product page

## Relationships

- Un **Gate PR** contiene molti **Controlli rapidi** e zero o più **Build native**
- L'**Esito PR** riassume un **Gate PR**
- Una **Release** ha un **Canale** e un **Numero di build** più alto di ogni Release precedente
- Una **Release** del Canale `beta` aggiorna solo la **Pagina download** beta
