# CODEBASE-STATE — Educational Tech Stack (V2 baseline)

**Date**: 2026-09-12
**Branch**: `main` -> `origin/main` (clean, 1 commit: `dc70fff` — surgical-implementation V2 run-003)

## Run Metadata

- Host: `ewaldt-N95`, user `ewaldt`, Linux 7.0.0-31-generic
- Node v24.15.0 / npm 12.0.2 / Python 3.11.15
- `node_modules/`: **absent** (0 entries)
- `docs/`: **absent** (created this run)

## Tech Stack (declared vs actual)

| Layer | Declared | Actual |
|-------|----------|--------|
| JS test | jest ^29.7.0 | **not installed** |
| JS lint | eslint ^8.50.0 | **not installed, no config** |
| JS format | prettier ^3.0.3 | **not installed** |
| Static server | live-server ^1.2.2 | **not installed** |
| Runtime deps | axios, chart.js, bootstrap | **not installed** |
| Python | requirements.txt (flask, django, pandas, tensorflow, ...) | **none installed** |

## Repository Structure (20 files, all tracked, clean tree)

```
.
├── AUDIT_REPORT.md            # prior audit (2025-08-13)
├── JOB_CARD.md                # repo job card / history
├── README.md                  # curriculum README
├── PROGRAM_SUMMARY.md         # learning program summary
├── PROGRAM_SUMMARY_OLD.md     # superseded summary
├── prerequisites.md           # hardware/software prerequisites
├── setup-guide.md             # setup instructions
├── curriculum-overview.md     # curriculum map
├── package.json               # npm manifest (scripts + deps)
├── requirements.txt           # python deps (uninstalled)
├── .gitignore                 # already ignores docs/.scratch-audit/
├── .vscode/settings.json      # python env settings
├── .snapshots/                # GBTI snapshots-for-ai tooling (config, readme, sponsors)
├── scripts/
│   └── fetch_video_transcript.py   # youtube_transcript_api wrapper
├── modules/
│   ├── 01-programming-fundamentals/README.md   # core module (video-aligned)
│   └── 02-web-development/README.md            # archived / out-of-scope
└── exercises/
    ├── python-basics/
    │   ├── exercise_01_personal_info.py     # STUB (TODOs, collect_personal_info returns None)
    │   ├── exercise_02_grade_calculator.py  # STUB (all functions return None/pass)
    │   └── exercise_03_adventure_game.py    # PARTIAL (class scaffold, 1 location, TODOs)
    ├── web-basics/
    │   └── interactive-dashboard.html        # COMPLETE (working JS: calc, grades, notes, quiz, editor)
    └── full-stack-projects/
        └── exercise-04-pvt-class-tracker.md   # SPEC ONLY (6-week project, no code)
```

## Component Classification

| Component | Status | Notes |
|-----------|--------|-------|
| `modules/01-programming-fundamentals/README.md` | **Complete** | Curriculum doc, video-aligned |
| `exercises/web-basics/interactive-dashboard.html` | **Complete** | Runnable single-file app |
| `exercises/python-basics/exercise_01` | **STUB** | Student TODO scaffold |
| `exercises/python-basics/exercise_02` | **STUB** | Student TODO scaffold |
| `exercises/python-basics/exercise_03` | **PARTIAL** | Scaffold + 1 location; expansion is student task |
| `exercises/full-stack-projects/exercise-04` | **SPEC** | Design doc, no implementation exists |
| `package.json` toolchain | **BROKEN** | Scripts declared, binaries absent |
| `requirements.txt` | **NOT INSTALLED** | Python deps uninstalled |

## Baseline Test Evidence (live, 2026-09-12)

| Command | Result |
|---------|--------|
| `npm test` | `sh: 1: jest: not found` — exit **127** |
| `npm run lint` | would fail (eslint not installed, no config) |
| `python3 exercise_01` | runs, prints prompt, exits 0 (stub `pass`) |
| `python3 exercise_02` | runs, "No valid scores entered", exits 0 (stub) |
| `python3 exercise_03` (input `quit`) | runs game loop, exits 0 (scaffold functional) |

## Known Issues

1. **KI-01**: Declared npm toolchain (jest/eslint/prettier/live-server) is non-functional —
   `node_modules/` absent. This is the primary actionable defect.
2. **KI-02**: No eslint configuration file exists; `npm run lint` would fail even after install
   unless a config is added.
3. **KI-03**: `requirements.txt` lists heavy ML/data deps (tensorflow, torch, scikit-learn)
   that are not installed and are out of scope for the video-aligned stack.
4. **KI-04**: Student exercise stubs are intentionally incomplete (teaching scaffolds).

## Initial Risks

| ID | Risk | Severity |
|----|------|----------|
| RISK-01 | Installing npm deps may pull large transitive trees (jest ~200MB) | Low |
| RISK-02 | Network unavailable -> toolchain fix blocked -> report honestly | Medium |
| RISK-03 | Fabricating student solutions would corrupt the curriculum's purpose | High (avoided) |
