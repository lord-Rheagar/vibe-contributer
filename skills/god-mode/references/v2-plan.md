# God-Mode v2 Plan

## Full Autonomous Pipeline

God-mode in v2 will:

1. **Search** — run search-repo and search-issues autonomously, selecting a repo and issue based on the same filters as the guided flow.

2. **Plan** — run planning-solution to produce a plan. If the plan has critical ambiguities, iterate or pick a different issue.

3. **Build** — run build with embedded critique. If verification gates fail, iterate on the fix up to a defined iteration limit (default: 3).

4. **Pause for human confirmation** — present to the user:
   - Plan summary (what the fix does)
   - Diff scope (files changed, lines)
   - Verification results (tests, linter, self-critique)
   - Any trade-offs or areas to review

5. **Push** — only after the user explicitly approves, run push-PR.

## Safety Gates (all must pass before presenting to user)

- Tests pass (or no test suite exists — degraded pipeline noted)
- Linter clean (or no linter exists — noted)
- Diff scope reasonable (focused, < 10 files, < 500 lines)
- Self-critique completed with no unresolved critical findings

## Configurable Autonomy (future, post-v2)

- User-settable checkpoints: none, after-planning, after-build, before-push
- Configurable iteration limit
- Configurable diff scope thresholds
