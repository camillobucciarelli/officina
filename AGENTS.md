## Agent skills

### Issue tracker

Issues live in GitHub Issues for `camillobucciarelli/officina`, accessed with the `gh` CLI. See `docs/agents/issue-tracker.md`.

### Triage labels

Uses the default labels: bug, enhancement, needs-triage, needs-info, ready-for-agent, ready-for-human, wontfix. See `docs/agents/triage-labels.md`.

### Domain docs

Single-context: one `GLOSSARY.md` at the root, with ADRs in `docs/adr/`. See `docs/agents/domain.md`.

### External peers

`auto`: uses the Claude or Codex adapter, picked from host-family metadata. See `docs/agents/cross-model.json`.
