# Repo Search Filters

## Contributor-Friendliness Signals

When searching for repos, filter for these signals that a repo welcomes outside contributions:

### GitHub Search Query

```bash
gh search repos \
  --stars=">100" \
  --updated=">2026-06-01" \
  --language="[optional language]" \
  --topic="[optional topic]" \
  --limit=20 \
  "[keywords]"
```

### Manual Checks (for each promising result)

After getting search results, verify these signals for the top candidates:

1. **`good first issue` label exists** — run `gh label list --repo owner/repo` and check for `good first issue` or `good-first-issue`
2. **`CONTRIBUTING.md` exists** — check repo root for contributing guidelines
3. **Active maintenance** — check that the last commit was within the last 30 days via `gh api repos/owner/repo/commits --jq '.[0].commit.committer.date'`
4. **Open issues with maintainer engagement** — check that issues have recent comments from maintainers
5. **CI/CD configured** — check for `.github/workflows/` or equivalent CI config

### Scoring

Prioritize repos that have ALL of:
- `good first issue` label
- `CONTRIBUTING.md`
- Active commits in last 30 days
- CI/CD configured

Repos missing CI/CD or CONTRIBUTING.md are still viable but the build and planning-solution skills will need to work harder.
