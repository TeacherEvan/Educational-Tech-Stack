# RISK - Educational Tech Stack (V2)

**Run**: surgical-impl-20260912-ets / run-002
**Date**: 2026-09-12

## Risk Register

| ID | Risk | Severity | Likelihood | Mitigation | Status |
|----|------|----------|------------|------------|--------|
| RISK-01 | npm install pulls large transitive tree (~555 packages) | Low | Certain | node_modules/ gitignored; disk space monitored | Accepted |
| RISK-02 | Network unavailable blocks toolchain install | Medium | Low | Install succeeded on first attempt; no retry needed | Accepted |
| RISK-03 | Fabricating student solutions corrupts curriculum | High | N/A | Student stubs left untouched (C-01) | Avoided |
| RISK-04 | ESLint v10 vs .eslintrc.json format incompatibility | Medium | Certain | Installed eslint@8.57.1 (declared); .eslintrc.json compatible | Mitigated |
| RISK-05 | Global eslint@10 on PATH shadows local binary | Low | Certain | npm run lint uses local node_modules/.bin/eslint via npm script shim | Mitigated |

## Residual Risk

- None blocking. All identified risks accepted or mitigated.
- Remaining known issue: requirements.txt lists heavy ML deps (tensorflow, torch) not installed - out of scope for this run (KI-03).

## Verdict

ACCEPTABLE - no blocking risks
