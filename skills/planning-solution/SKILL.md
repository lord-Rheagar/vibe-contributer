---
name: planning-solution
description: Read a selected issue and the codebase, then propose a detailed fix plan a non-coder can evaluate. Reads contributing guidelines and existing patterns to align the solution with how the project works.
argument-hint: "[optional: issue number override]"
---

# Planning Solution

Read the selected issue, the repository's contributing guidelines, and enough of the codebase to propose a detailed fix scoped to the issue. The plan must be readable by someone who cannot read code.

## Workflow

1. **Read context.** Read `.vibe-contributer/context.json` to get `repo_url`, `repo_name`, `issue_number`, and `issue_title`. If any are missing, direct the user to run the preceding skills and stop.

2. **Read the issue.** Use `gh issue view <number> --repo <owner/repo> --json body,comments,labels` to get the full issue body and comments. Understand what the problem is and what "done" looks like.

3. **Read contributing guidelines.** Check for `CONTRIBUTING.md`, `CONTRIBUTE.md`, or `.github/CONTRIBUTING.md` in the repo root. Read it. If none exists, proceed with general OSS conventions and note the absence. See `references/contributing-guideline-checklist.md` for what to look for.

4. **Scan the codebase.** Identify:
   - The file structure and where the relevant code lives
   - Naming conventions (files, functions, classes)
   - Test conventions (test framework, test file locations, test naming)
   - Linting/formatting setup (eslint, prettier, ruff, gofmt, etc.)
   - Existing patterns for similar fixes (grep for similar code)

5. **Propose a plan.** Write a plan document using the template in `references/plan-template.md`. The plan must include:
   - What the fix does (in functional terms, not code)
   - Why this approach (alternatives considered, why this one)
   - What files it touches (file paths and why)
   - What the verification plan is (which tests to run, what to check)
   - Any trade-offs or areas the maintainer should review closely

6. **Write context.** Save the plan to a file (e.g., `.vibe-contributer/plan.md`) and update `.vibe-contributer/context.json` with `plan_path`.

7. **Direct to next step.** Present the plan to the user for review. After the user approves, suggest invoking `build` to implement.

## Error Handling

- Issue is vague or lacks acceptance criteria: note this, propose your best interpretation, flag the ambiguity in the plan.
- No CONTRIBUTING.md: proceed with general conventions, note in the plan.
- Issue references files that don't exist: report the mismatch, suggest the issue may be stale.
- Codebase is too large to scan fully: focus on the directory most likely to contain the relevant code based on the issue description.
