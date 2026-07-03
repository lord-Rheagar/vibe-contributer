---
name: critique
description: Analyze, critique, and verify a diff or PR against the non-slop verification pipeline. Multi-stage: tests, CI, diff scope, structured self-review. Invokable standalone or embedded in build.
argument-hint: "[optional: PR number or branch name]"
---

# Critique

Run a multi-stage verification pipeline on a diff, branch, or PR. Produces a structured report a non-coder can read and judge. Invokable standalone on any diff, or embedded inside build (where its output feeds the next gate instead of requiring human interpretation).

## Modes

- **Standalone:** User invokes critique directly on any branch, diff, or PR. No contribution context required. Produces a report the user reads and acts on.
- **Embedded:** Called from inside build or god-mode. Uses the same pipeline but output feeds the next gate. See `references/verification-pipeline.md` for the shared pipeline definition.

## Workflow (Standalone)

1. **Determine what to critique.** If a PR number is provided, use `gh pr diff <number>` to get the diff. If a branch name is provided, use `git diff main...<branch>`. Otherwise, use `git diff` on the current working tree.

2. **Load the verification pipeline.** Read `references/verification-pipeline.md` for the full multi-stage pipeline definition.

3. **Run each stage:**
   - **Local tests** — run the project's test suite. These are the primary external ground truth.
   - **CI results** (if on an existing PR) — use `gh pr checks <number>` to get CI status. This is secondary ground truth; local tests are primary when no PR exists yet.
   - **Diff scope checks** — verify the diff is focused, follows conventional commit format, matches existing patterns.
   - **Structured self-review** — produce a written analysis covering correctness, edge cases, style, and scope.

4. **Produce the report.** Write a structured report with:
   - Overall verdict: PASS / FAIL / NEEDS_ATTENTION
   - Per-stage results (pass/fail/skipped + evidence)
   - Critical findings (if any) — things that must be fixed before pushing
   - Minor findings — things worth noting but not blocking
   - A summary a non-coder can understand: "The fix looks good. Tests pass. The diff is small and focused. One thing to watch: the error message format differs from existing patterns."

5. **Write context (if available).** If `.vibe-contributer/context.json` exists, update `verification_results` with the critique output.

## Workflow (Embedded)

When embedded in build or god-mode:
1. Load the same `references/verification-pipeline.md`.
2. Run the same stages, but substitute local test execution for CI (no PR exists yet during build).
3. Return pass/fail + evidence to the calling skill. Do not require human interpretation.
4. If a critical finding is unresolved, return FAIL — the calling skill must iterate before proceeding.

## Error Handling

- No tests, no linter in repo: degrade to diff-scope + self-critique only. Note in report.
- Self-critique finds a critical issue: flag it prominently as unresolved. Report does not pass.
- `gh pr checks` fails (no PR, no CI): skip CI stage, note in report.
- Diff is empty: report "nothing to critique" and stop.
