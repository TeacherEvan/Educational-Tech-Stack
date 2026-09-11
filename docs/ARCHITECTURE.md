# ARCHITECTURE — Educational Tech Stack (V2)

**Date**: 2026-09-12

## Current -> Target

| Area | Current | Target | Delta |
|------|---------|--------|-------|
| `package.json` toolchain | devDeps declared, `node_modules/` absent | `node_modules/` installed; `npm test`/`lint`/`format` runnable | install + add eslint config |
| `exercises/python-basics/*` | Stubs (TODOs) | Stubs (unchanged) | **no change** (student work) |
| `exercises/web-basics/*` | Complete HTML app | Complete (unchanged) | **no change** |
| `exercises/full-stack-projects/*` | Spec only | Spec only (unchanged) | **no change** |
| `docs/` | Absent | `REQUIREMENTS`, `CODEBASE-STATE`, `ARCHITECTURE`, `TODO` + audit dir | created this run |
| `modules/*` | Complete | Complete (unchanged) | **no change** |

## Areas Being Edited

Only **one** area is modified in this run:

1. **`package.json` + `.gitignore` + `docs/`** — toolchain enablement and audit artifacts.

No source code in `exercises/` or `modules/` is touched.

## Interfaces / Dependencies

- `npm test` -> `jest` binary (declared in `package.json` devDependencies)
- `npm run lint` -> `eslint` binary + config (declared; config missing)
- `npm run format` -> `prettier` binary (declared)
- `npm run start` / `npm run dev` -> `live-server` binary (declared)
- Python exercises -> CPython 3.11 stdlib only (no external deps)

## Data / Control Flow

```
user request -> plan scan (no plans) -> author fresh plan
  -> CONSISTENCY_GATE (reviewer PASS)
  -> IMPLEMENT (toolchain install + eslint config + audit docs)
  -> VERIFY (npm test/lint/format + python exec + HTML parse)
  -> SECURITY_AUDIT (no secrets in scope)
  -> FINAL_AUDIT -> DEBRIEF -> HANDOFF -> COMPLETE
```

## Security Boundaries

- No credentials, tokens, or secrets are in scope for this repo.
- `requirements.txt` and `package.json` contain no secrets.
- Audit artifacts are written under `docs/.scratch-audit/` (gitignored).

## AC Mapping

| AC | Area | Evidence |
|----|------|----------|
| AC-001 | `npm test` | live `npm test` output |
| AC-002 | `npm run lint` | live `npm run lint` output |
| AC-003 | `npm run format` | live `npm run format` output |
| AC-004 | Python exercises | `python3 <file>` each |
| AC-005 | HTML dashboard | structural parse |
| AC-006 | `docs/` artifacts | `ls docs/` |
| AC-007 | git state | `git status` |
