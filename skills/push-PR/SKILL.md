---
name: push-PR
description: Verify evidence exists, write a detailed PR description, and ship the PR. Refuses to push if no verification evidence is found. Tracks PR status after push.
argument-hint: "[optional: PR title override]"
---

# Push PR

Verify that critique/verification evidence exists before proceeding. Write a detailed PR description a non-coder would write. Create the PR. Track its status.

## Workflow

1. **Read context.** Read `.vibe-contributer/context.json` to get `verification_results`, `build_branch`, `issue_number`, `repo_url`, and `plan_path`.

2. **Verify evidence exists.** Check that `verification_results` is present and non-empty. If no evidence is found, **refuse to push** and direct the user to run `build` or `critique` first. This is the non-slop gate — no PR goes out without verification.

3. **Write the PR description.** Use the template in `references/pr-description-template.md` to write a PR description that includes:
   - What issue it addresses (with `Closes #<number>` if applicable)
   - What the fix does in functional terms
   - How it was verified (test results, linter results, diff scope)
   - Trade-offs or areas the maintainer should review closely

4. **Create the PR.** Use `gh pr create` with:
   - Title: conventional commit format from the plan (e.g., `fix: validate email before submission`)
   - Body: the PR description from step 3
   - Base: the repo's default branch

5. **Write context.** Update `.vibe-contributer/context.json`:
   ```json
   {
     "pr_url": "https://github.com/owner/repo/pull/123",
     "pr_status": "open"
   }
   ```

6. **Start PR tracking (R17).** After push, check PR status periodically:
   ```bash
   gh pr view <number> --json state,mergedAt,closedAt
   ```
   Update `pr_status` in context.json: `open`, `merged`, `closed`, `changes-requested`.

7. **Report to user.** Present the PR URL and a summary. Remind the user that success is measured at PR-merged, not PR-opened.

## Error Handling

- No verification evidence in context.json: refuse to push. Direct user to run `build` or `critique`. Stop.
- `gh pr create` fails (permissions, branch protection): report the error, suggest alternatives (fork workflow, different branch).
- Already on default branch: create a feature branch first before pushing.
- Remote not configured: report and suggest `git remote add origin <url>`.
