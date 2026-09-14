# TODO — Educational Tech Stack (V2 implementation plan)

**Date**: 2026-09-12
**Plan type**: Fresh-authored (no prior plans found in repo)

## Objectives

| ID | Objective | Requirement | Area | Acceptance | Status |
|----|-----------|-------------|------|------------|--------|
| OBJ-001 | Install declared npm devDependencies (jest, eslint, prettier, live-server) | FR-01/02/03 | `package.json` | `node_modules/` populated; binaries on PATH | [x] |
| OBJ-002 | Add minimal eslint config so `npm run lint` runs without "command not found" | FR-02 | repo root | `npm run lint` exits 0 or reports lint errors, not 127 | [x] |
| OBJ-003 | Make `npm test` runnable (jest discovers 0 test files, exits honestly) | FR-01 | `package.json` | `npm test` exit != 127; reports "no tests found" or runs existing tests | [x] |
| OBJ-004 | Make `npm run format` runnable (prettier on declared glob) | FR-03 | `package.json` | `npm run format` exit != 127 | [x] |
| OBJ-005 | Verify Python exercise 01 executes without traceback | FR-04 | `exercises/python-basics/` | `python3 exercise_01` exit 0 | [x] |
| OBJ-006 | Verify Python exercise 02 executes without traceback | FR-04 | `exercises/python-basics/` | `python3 exercise_02` exit 0 | [x] |
| OBJ-007 | Verify Python exercise 03 executes without traceback | FR-04 | `exercises/python-basics/` | `python3 exercise_03` exit 0 | [x] |
| OBJ-008 | Structural check on HTML dashboard (balanced tags, loadable) | FR-05 | `exercises/web-basics/` | no unbalanced `<div>`/`<script>` in main structure | [x] |
| OBJ-009 | Create `docs/.scratch-audit/` runtime artifacts (manifest, state, events) | FR-06 | `docs/` | `runtime/manifest.json`, `state.json`, `events.jsonl` exist | [x] |
| OBJ-010 | Produce TRACEABILITY.md mapping objectives -> requirements -> evidence | AC-006 | `docs/` | `docs/TRACEABILITY.md` exists with full mapping | [x] |
| OBJ-011 | Produce SECURITY.md + RISK.md audit outputs | AC-006 | `docs/` | `docs/SECURITY.md`, `docs/RISK.md` exist | [x] |
| OBJ-012 | Produce 17-section debrief.md | AC-006 | `docs/` | `docs/debrief.md` exists with all 17 sections | [x] |
| OBJ-013 | Commit working-tree changes; push only if `PUSH=1` | AC-007 | repo | `git status` clean after commit; push conditional | [x] |

## Definition of Done

- All objectives above are `[x]` with live tool evidence (run-003).
- `npm test`, `npm run lint`, `npm run format` all exit != 127.
- All 3 Python exercises exit 0.
- HTML dashboard structurally valid.
- Audit artifacts present under `docs/`.
- No student `# TODO:` stubs were filled in (NFR-02).
- No fabricated results; every claim backed by live output.
