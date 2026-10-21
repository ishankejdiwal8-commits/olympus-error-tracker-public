# Error history

## Olympus Parquet challenge

Context recovered from the archived Codex task **Find a qualifying GitHub repo**. The challenge targets `parquet-go/parquet-go` at base commit `144b6469bf93ade1ffc4610a56baadb7a08b9808` and proposes safe schema-conversion widening from INT32 to INT64 and FLOAT to DOUBLE.

The imported history contains task-quality, prompt-quality, test-design, build, solution-quality, and patch-application findings. See [`errors/olympus-challenge-history.md`](errors/olympus-challenge-history.md) for details and [`errors/index.csv`](errors/index.csv) for the issue index.

### Latest known state

- The most recent Verify Solution report still failed to apply `solution.patch` at `convert.go:1010`.
- Earlier patch revisions failed at `convert.go:588`, `591`, `1007`, and `1009`, because hunk context/line positions did not match the challenge source. A patch was regenerated against the stated base commit, but a later verifier run still failed at line 1010.
- The quality review found missing decimal scale/precision protection and page dictionary/data type inconsistencies. A patch revision was reported to address them, but no successful Go test run is recorded.
- Several test and prompt revisions were made, but no final all-green verifier result is recorded.

## Imported reference material

The Desktop `error.md` was copied to [`docs/legacy-challenge-rejections.md`](docs/legacy-challenge-rejections.md). It describes generic challenge-submission rejection patterns and does not identify Olympus or Parquet. It remains unverified reference material, not a confirmed Olympus incident.

## Import note

This history was added on 2026-10-21. The archived task does not provide exact dates for each individual evaluator finding, so the CSV date is the import date where an original date was unavailable.
