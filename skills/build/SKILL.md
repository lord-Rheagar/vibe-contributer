---
name: build
description: Implement the planned solution with embedded verification gates. Reads the plan, writes the fix, runs tests/linters, checks diff scope, and produces structured self-critique. Build is not done until verification evidence exists.
argument-hint: "[optional: plan path override]"
---

# Build

Implement the planned solution following the contributing guidelines and existing code patterns. Run embedded verification before returning — the build is not "done" until verification evidence exists.

## Workflow

1. **Read context.** Read `.vibe-contributer/context.json` to get `plan_path`, `repo_url`, and `issue_number`. If `plan_path` is missing, direct the user to run `planning-solution` first and stop.

2. **Read the plan.** Read the plan document at `plan_path`. Understand what to implement, what files to touch, and what the verification plan says.

3. **Create a feature branch.** If not already on one, create a branch following the repo's naming conventions (or default: `fix/issue-<number>-<short-description>`).

4. **Implement the fix.** Write the code following:
   - The plan's approach
   - The repo's contributing guidelines
   - Existing code patterns and conventions
   - Naming and style conventions discovered during planning

5. **Run embedded verification.** Load the shared verification pipeline from `../critique/references/verification-pipeline.md` and run it:
   - Execute existing tests
   - Run linters/formatters if the project has them
   - Check diff scope is reasonable (focused, not touching unrelated files)
   - Produce a structured self-critique covering correctness, edge cases, style, and scope

6. **Record results.** Update `.vibe-contributer/context.json`:
   ```json
   {
     "build_state": "success",
     "build_branch": "fix/issue-42-typo",
     "verification_results": {
       "tests": {"passed": true, "output": "..."},
       "linter": {"passed": true, "output": "..."},
       "diff_scope": {"files_changed": 2, "reasonable": true},
       "self_critique": {"critical_findings": [], "notes": "..."}
     }
   }
   ```

7. **Direct to next step.** If `build_state` is `success`, suggest invoking `push-PR` to ship. If `build_state` is `failed`, report the failures and suggest iterating.

## Error Handling

- Tests fail after implementation: report the failures, attempt to fix them, re-run. If still failing after 2 attempts, report and let the user decide.
- No test suite in target repo: run diff-scope check and self-critique only. Note the degraded pipeline in verification results.
- Diff touches 40+ files: flag as a red flag, ask the user to review before proceeding.
- Build fails due to missing dependencies: report the error, suggest setup steps from CONTRIBUTING.md.
- Linter not found: skip linter check, note in verification results.
