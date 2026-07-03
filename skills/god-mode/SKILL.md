---
name: god-mode
description: Coming in v2. Will run the full pipeline autonomously — search, plan, build, critique, push — with a human confirmation gate before the irreversible push action.
argument-hint: ""
---

# God Mode (Coming in v2)

God-mode is deferred to v2. It will run the full contribution pipeline autonomously:

1. `search-repo` — find a contributor-friendly repo
2. `search-issues` — find a workable issue
3. `planning-solution` — plan the fix
4. `build` — implement with embedded critique
5. **Pause** — present the PR draft (plan summary, diff scope, verification results, self-critique findings) for human approval
6. `push-PR` — ship only after explicit user confirmation

## Why the human confirmation gate?

A public PR to a repo the user does not own is irreversible. The self-critique gate alone cannot substitute for human judgment because the same LLM reviewing its own work shares systematic blindspots. The human confirmation gate preserves the autonomy benefit (no manual step-by-step orchestration) while adding human judgment at the only irreversible action.

## For v1

Use the guided flow instead:

```text
search-repo → search-issues → planning-solution → build → critique → push-PR
```

Each skill is invoked manually, with the user making decisions at each checkpoint.
