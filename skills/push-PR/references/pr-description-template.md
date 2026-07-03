# PR Description Template

Use this structure when writing a PR description. The description should be readable by both the maintainer and the non-coder who is shipping the PR.

## Template

```markdown
## Description

Closes #<issue_number>

<1-2 sentence summary of what this PR does, in functional terms. Example: "Adds email validation to the signup form so invalid emails show a friendly error instead of crashing.">

## What changed

<Bullet list of changes, in plain language. No code.>
- Added a check for valid email format before form submission
- Added a helper function to validate email strings
- Updated the error message to be user-friendly

## How it was verified

- **Tests:** <passed/failed/skipped> — <command run, e.g., `pytest`>
- **Linter:** <passed/failed/skipped> — <command run, e.g., `eslint`>
- **Diff scope:** <number> files changed, <number> lines — <reasonable/not reasonable>
- **Self-critique:** <no critical findings / N critical findings resolved>

## Trade-offs / Review notes

<Things the maintainer should look at closely. Be honest.>
- This doesn't handle unicode emails — may want full RFC 5322 validation later
- The error message format is new — verify it matches the project's style
- No new tests were added because the existing test suite covers this path

## Checklist

- [x] Tests pass locally
- [x] Linter passes (if applicable)
- [x] Diff is focused and small
- [x] Contributing guidelines followed
```

## Tone

Write the description as if you are the contributor (the non-coder), not as the AI agent. Use first person: "I added..." not "The agent implemented...". The PR represents the user's contribution, not the agent's.
