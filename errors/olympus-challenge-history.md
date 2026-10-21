# Olympus challenge evaluator and error history

Imported from the archived task **Find a qualifying GitHub repo** on 2026-10-21. Individual event dates were not retained in the archived transcript; rows in `index.csv` use the import date where needed. Status reflects the last evidence found, not an assumption that a fix passed.

## OLY-0001 — Initial repository was reserved

**Finding:** The first candidate, `go-git/go-git`, was rejected by the Olympus platform as reserved to prevent data contamination.

**Response:** The task was redirected to `parquet-go/parquet-go` at base commit `144b6469bf93ade1ffc4610a56baadb7a08b9808`.

**Status:** `closed` — a different repository was selected.

## OLY-0002 — Initial task was too small for Olympus scope

**Finding:** The initial task was a localized conversion predicate and reuse of existing value conversion. The Task Quality review said it lacked the long-horizon, multi-path work expected for Olympus.

**Response:** The prompt was expanded to require consistent behavior across row conversion, row groups/pages, file copying, reported types/bounds, and supported logical annotations.

**Status:** `investigating` — scope was expanded, but no final clean task-quality verdict is recorded.

## OLY-0003 — Task prompt was repeatedly too prescriptive

**Finding:** Multiple Task Prompt Quality checks rejected wording for focusing on implementation details instead of desired behavior.

**Response:** The prompt was rewritten in scenario-based and behavior-focused forms. The archived history does not contain a subsequent passing Task Prompt Quality result.

**Status:** `open` — final acceptance is unverified.

## OLY-0004 — Test coverage missed required paths and metadata

**Finding:** Early tests covered direct in-memory row conversion but omitted row-group/page access, copy-to-file round trips, converted type metadata, and value bounds. Review also called out signedness and TIME/TIMESTAMP/DECIMAL behavior.

**Response:** The test patch was expanded for row groups/pages, file copying, bounds, and logical-type rejection; the solution patch was expanded for page data and metadata.

**Status:** `investigating` — no complete successful test and solution verification is recorded.

## OLY-0005 — Test patch contained compile and brittleness issues

**Finding:** Go tests used integer range loops that did not compile in the verifier's Go version. Review also flagged a requirement for multiple pages, a misleading `-math.MaxFloat32` case label, and brittle base-test selection.

**Response:** The patch changed to index-based loops, allowed a single page, clarified the case label, and added a base-selection fallback.

**Status:** `investigating` — patch edits were reported, but the Go test suite was not rerun locally.

## OLY-0006 — Predictable test filename collision

**Finding:** `numeric_promotion_test.go` matched a path likely to be independently chosen by an implementer.

**Response:** The test filename was given random suffix `18ce95`.

**Status:** `fixed` — the rename was recorded; no later collision warning appears.

## OLY-0007 — Baseline count included the new task tests

**Finding:** Baseline reports showed 19, then 20 failures, matching the number of newly added task tests. The baseline was expected to run against a clean repository without those tests, so the report did not establish pre-existing failures.

**Response:** `test.sh` base mode was changed to exclude the new `TestNumericPromotion` cases.

**Status:** `investigating` — the adjustment was not followed by a fresh authoritative baseline run.

## OLY-0008 — Several new tests passed without the solution

**Finding:** Baseline verification reported six widening, nested-optional, and added/removed-column tests passing without `solution.patch`, so they did not prove the new implementation was needed.

**Response:** Those tests were revised to also assert that narrowing is rejected.

**Status:** `investigating` — no rerun confirms the intended fail-to-pass behavior.

## OLY-0009 — Logical-type test failed with the solution

**Finding:** `TestNumericPromotionRejectsIncompatibleLogicalTypes` failed. The prior diagnosis was that the test passed `parquet.Group` to `SchemaOf`, which accepts structs and could panic.

**Response:** The test was changed to build explicit DATE and INT64 schemas with `NewSchema`.

**Status:** `investigating` — no successful Verify Solution result is recorded after the change.

## OLY-0010 — Solution quality gaps for decimals and converted pages

**Finding:** The Solution Quality review identified two high-comprehensiveness issues:

- BYTE_ARRAY-backed decimal conversions with changed scale/precision could pass through unchanged and be reinterpreted as a different value.
- A promoted page reported the target type while `Dictionary()` and `Data()` still exposed source-typed contents; bounds/statistics also needed to match values returned to readers.

**Response:** The patch was reported to reject decimal logical-type changes and convert page data, dictionaries, indexes, and related metadata.

**Status:** `investigating` — no Go test run or final quality pass is recorded.

## OLY-0011 — Solution patch repeatedly failed to apply

**Finding:** Verify Solution repeatedly reported `solution.patch` failing to apply to `convert.go`, at lines 588, 591, 1007, 1009, and finally 1010. Earlier edits shifted hunk positions without matching the challenge's exact source layout.

**Response:** The user supplied base commit `144b6469bf93ade1ffc4610a56baadb7a08b9808`; the patch was regenerated against that revision and was reported to apply after `test.patch`.

**Latest status:** `open` — a later Verify Solution report still failed at `convert.go:1010`. No later passing application result is present in the archived task.

## OLY-0012 — Container rebuild failed without diagnostic details

**Finding:** The platform reported that the image failed to rebuild, but the message did not include the failing Docker step or log. The task workspace had no source checkout or Dockerfile available for diagnosis.

**Response:** The prior task requested the full build log.

**Status:** `open` — no failing step or confirmed fix was found in the transcript.

## OLY-0013 — Task category review suggested bugfix

**Finding:** The task was initially labeled `feature_request`; a later classification review said it described a correctness/compatibility bug and recommended `bugfix`.

**Response:** The task editor was not available. `bugfix` was copied to the clipboard, but no platform update is recorded.

**Status:** `open` — category change is unverified.

## Current follow-up order

1. Regenerate `solution.patch` from the exact challenge base and test patch, then verify a clean apply.
2. Run the full Go test matrix with the solution and confirm all new tests pass and fail-to-pass tests fail without the solution.
3. Rerun the baseline with task tests excluded.
4. Rerun prompt quality and Olympus task quality checks.
5. Obtain the rebuild log and resolve the failing container step.
