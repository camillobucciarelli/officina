# officina
Workflow GitHub Actions condivisi da copista, postino e custode

## Gate PR (`pr.yml`)

`.github/workflows/pr.yml` è il Gate PR riusabile delle app Flutter: si chiama con `workflow_call`, senza input e senza secret, e lavora sul repo chiamante. Quattro job:

- `controlli`: `dart format`, codice generato committato, `flutter analyze --fatal-infos`
- `test`: i test senza il tag `golden`, su Linux
- `golden`: i test con il tag `golden`, su macOS
- `sicurezza`: osv-scanner su `pubspec.lock` e gitleaks

### Contratto del repo chiamante

- `.fvmrc` alla radice: è l'unica fonte della versione di Flutter.
- `tool/ci/codegen.sh` (facoltativo, eseguibile): rigenera il codice. Se dopo resta un diff, il job fallisce.
- `tool/ci/test.sh` (facoltativo, eseguibile): sostituisce `flutter test`. Riceve `--exclude-tags golden` oppure `--tags golden` e deve inoltrarli a `flutter test`.
- I test golden portano il tag `golden`. Se nessun file sotto `test/` o `packages/*/test/` lo contiene, il job `golden` passa con un avviso.

### Esempio di chiamata

Il job `pr-ok` sta nel chiamante: è l'unico controllo da rendere obbligatorio nella protezione del ramo.

```yaml
name: PR

on:
  pull_request:

permissions:
  contents: read
  pull-requests: read # richiesto da gitleaks nel job `sicurezza`

jobs:
  gate:
    uses: camillobucciarelli/officina/.github/workflows/pr.yml@<sha>

  # Segnaposto: i controlli propri dell'app.
  app:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v7
      - run: echo "controlli specifici dell'app"

  pr-ok:
    if: always()
    needs: [gate, app]
    runs-on: ubuntu-latest
    steps:
      - run: jq -e 'all(.[]; .result == "success" or .result == "skipped")' <<< '${{ toJSON(needs) }}'
```

Un workflow chiamato non può avere più permessi di quelli concessi dal chiamante: il chiamante deve concedere `contents: read` e `pull-requests: read`.
