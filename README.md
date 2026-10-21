# Olympus Error Tracker

A small, append-only record of Olympus challenge errors, evaluator findings, fixes, and follow-up status.

## Recovered Olympus context

The archived task **Find a qualifying GitHub repo** identifies the challenge as `parquet-go/parquet-go` at base commit `144b6469bf93ade1ffc4610a56baadb7a08b9808`, targeting safe numeric promotion in schema conversion. See [`docs/olympus-task-context.md`](docs/olympus-task-context.md) and [`errors/olympus-challenge-history.md`](errors/olympus-challenge-history.md).

The latest recorded Verify Solution failure is a `solution.patch` application error at `convert.go:1010`. Other evaluator findings and patch revisions are recorded with their last known status; several were changed but never re-verified.

## Files

- [`ERROR_HISTORY.md`](ERROR_HISTORY.md): chronological summary and current status.
- [`errors/index.csv`](errors/index.csv): one row per prior evaluator/review issue.
- [`errors/olympus-challenge-history.md`](errors/olympus-challenge-history.md): detailed failure log with known fixes and unresolved follow-ups.
- [`templates/incident.md`](templates/incident.md): template for future incidents.
- [`docs/olympus-task-context.md`](docs/olympus-task-context.md): repository, commit, task scope, and provenance.
- [`docs/legacy-challenge-rejections.md`](docs/legacy-challenge-rejections.md): imported Desktop rejection notes. They are retained as unverified reference material and are not attributed to the Parquet task.

## Record a new incident

1. Copy `templates/incident.md` into a dated file such as `errors/2026-10-21-short-title.md`.
2. Record observed facts separately from suspected causes. Include exact error text when available.
3. Add one row to `errors/index.csv`. Leave the date blank if it is not known.
4. Record the fix and whether a clean verifier run confirmed it. Keep credentials and private personal data out of the repository.

Dates use `YYYY-MM-DD`. Severity: `critical`, `high`, `medium`, or `low`. Status: `open`, `investigating`, `fixed`, or `closed`.
