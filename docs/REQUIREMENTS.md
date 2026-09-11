# REQUIREMENTS — Educational Tech Stack (surgical-implementation V2 run)

**Run**: `surgical-implementation` / G&L Auditor V2
**Repo**: `TeacherEvan/Educational-Tech-Stack`
**Date**: 2026-09-12
**Request**: On-entry plan scan -> no plans found -> author fresh plan from current code state -> consistency-gate -> implement.

## Request Restatement

The repository is a **curriculum / learning-materials** repo, not an application.
It contains:

- 1 core module (`modules/01-programming-fundamentals/README.md`) mirroring The Coding Sloth's
  "Blazingly Fast Tech Stack" video (shadcn/ui + Clerk + Convex).
- 1 out-of-scope module (`modules/02-web-development/README.md` — archived).
- 3 Python exercises under `exercises/python-basics/` — **student stubs** with `# TODO:` markers.
- 1 HTML/CSS/JS dashboard (`exercises/web-basics/interactive-dashboard.html`) — **complete**, runnable.
- 1 full-stack capstone spec (`exercises/full-stack-projects/exercise-04-pvt-class-tracker.md`) —
  specification document only, no implementation.
- Root docs: `README.md`, `JOB_CARD.md`, `AUDIT_REPORT.md`, `PROGRAM_SUMMARY.md`,
  `PROGRAM_SUMMARY_OLD.md`, `prerequisites.md`, `setup-guide.md`, `curriculum-overview.md`.
- `package.json` declares npm scripts (`test`/`lint`/`format`/`start`/`dev`) and devDependencies
  (jest, eslint, prettier, live-server) **that are not installed** — `node_modules/` is absent.
- `requirements.txt` lists Python packages (flask, django, pandas, tensorflow, …) — none installed.

## Functional Requirements

| ID | Requirement | Source |
|----|-------------|--------|
| FR-01 | `npm test` must run and report a real result (currently `jest: not found`, exit 127) | `package.json` scripts |
| FR-02 | `npm run lint` must run (currently no eslint config / no installed binary) | `package.json` scripts |
| FR-03 | `npm run format` must run (prettier not installed) | `package.json` scripts |
| FR-04 | Python exercises must be importable / executable without crashing on the stub paths | `exercises/python-basics/*` |
| FR-05 | The HTML dashboard must be well-formed and loadable in a browser | `exercises/web-basics/interactive-dashboard.html` |
| FR-06 | Repository must have a `docs/` directory holding the V2 audit artifacts | skill mandate |

## Non-Functional Requirements

| ID | Requirement |
|----|-------------|
| NFR-01 | No fabricated results — every claim backed by live tool output |
| NFR-02 | Do **not** fill in student `# TODO:` stubs (they are teaching scaffolds, not deliverables) |
| NFR-03 | No secrets, no credentials exposed |
| NFR-04 | Budget <= USD 2.00, wall <= 30 min |
| NFR-05 | Push only if `PUSH=1` (set) |

## Constraints & Assumptions

- **C-01**: This repo is a curriculum, not a runnable app. "Implementation" scope is limited to
  making the **declared toolchain actually functional** and producing honest audit artifacts.
  The student exercise stubs are intentionally left incomplete — completing them would be
  fabricating educational content that is the student's task, not the repo owner's deliverable.
- **C-02**: `node_modules/` absent; network install required for jest/eslint/prettier.
- **C-03**: Python 3.11.15 available; Node v24.15.0 / npm 12.0.2 available.
- **C-04**: `docs/` directory does not yet exist; must be created.

## Acceptance Criteria

| ID | Criterion | Verification |
|----|-----------|--------------|
| AC-001 | `npm test` exits 0 or reports "no tests found" honestly (not 127) | `npm test` live output |
| AC-002 | `npm run lint` runs without "command not found" | `npm run lint` live output |
| AC-003 | `npm run format` runs without "command not found" | `npm run format` live output |
| AC-004 | All 3 Python exercises execute to their stub `pass`/prompt without traceback | `python3 <file>` each |
| AC-005 | HTML dashboard parses (no unbalanced tags in the main structure) | structural check |
| AC-006 | `docs/REQUIREMENTS.md`, `docs/CODEBASE-STATE.md`, `docs/ARCHITECTURE.md`, `docs/TODO.md` exist | `ls docs/` |
| AC-007 | Working tree committed; push only if `PUSH=1` | `git status` |
