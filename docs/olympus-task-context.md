# Recovered Olympus challenge context

## Source

Archived Codex task: **Find a qualifying GitHub repo** (created 2026-10-18, last updated 2026-10-20). The conversation records the task setup and multiple Olympus evaluator/reviewer results.

## Repository and base

- **Repository:** [`parquet-go/parquet-go`](https://github.com/parquet-go/parquet-go)
- **Base commit:** [`144b6469bf93ade1ffc4610a56baadb7a08b9808`](https://github.com/parquet-go/parquet-go/commit/144b6469bf93ade1ffc4610a56baadb7a08b9808)
- **Task title:** Add safe numeric promotion to schema conversion
- **Intended behavior:** Preserve values when reading INT32 columns as INT64 and FLOAT columns as DOUBLE, including nested/optional/repeated values and row-group/page/copy paths. Reject narrowing and logical-type changes that reinterpret values. Reported types and bounds should agree with values readers receive.

## Repository-selection history

An initial `go-git/go-git` suggestion was rejected by the platform as reserved to prevent data contamination. The task was redirected to `parquet-go/parquet-go`.

## Provenance

This context and the error log were reconstructed from the archived task transcript on 2026-10-21. Where an individual evaluator event had no timestamp, its index date is the import date. The separate `docs/legacy-challenge-rejections.md` came from the Desktop `error.md`; its relationship to this Parquet challenge is unknown.
