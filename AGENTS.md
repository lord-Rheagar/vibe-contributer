# Vibe Contributer — Agent Instructions

## What This Plugin Is

Vibe Contributer is a standalone plugin that encodes the "AI as junior engineer you manage carefully" methodology into structured skills for open-source contribution. It targets developer-adjacent contributors — people comfortable with git, CLI tools, and AI agents but not with reading or writing code.

## Skill Naming Convention

All skills use plain names without a prefix: `search-repo`, `search-issues`, `planning-solution`, `build`, `critique`, `push-PR`, `god-mode`.

## Contribution Context

State flows between skills via a JSON file at `.vibe-contributer/context.json` in the target repo's working directory. Fields:

- `repo_url` — selected repo URL (written by search-repo)
- `repo_name` — selected repo name (written by search-repo)
- `issue_number` — selected issue number (written by search-issues)
- `issue_title` — selected issue title (written by search-issues)
- `plan_path` — path to the plan document (written by planning-solution)
- `build_state` — success/failed (written by build)
- `build_branch` — branch name with the fix (written by build)
- `verification_results` — structured verification evidence (written by critique/build)
- `pr_url` — PR URL (written by push-PR)
- `pr_status` — open/merged/closed/changes-requested (written by push-PR, updated by R17 tracking)

Each skill reads the fields it needs on entry and writes its outputs before exiting.

## GitHub Operations

Skills use `gh` CLI for GitHub operations (search, issue reading, PR creation, PR status). Do not use direct API calls or manage tokens — `gh` handles authentication.

## Verification Pipeline

The shared verification pipeline lives in `skills/critique/references/verification-pipeline.md`. Both standalone critique and embedded critique (inside build) load this same reference to ensure identical criteria.

## Non-Slop Discipline

The plugin's core value is process discipline. Every skill must:
- Feed the agent real context (contributing guidelines, existing patterns, related files)
- Break asks into verifiable steps
- Use external verification (tests, CI, linters) as ground truth
- Produce output a non-coder can evaluate without reading code
- Treat agent output as a draft, not final
