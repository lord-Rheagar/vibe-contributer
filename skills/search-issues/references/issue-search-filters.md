# Issue Search Filters

## Priority Labels

When searching for issues, prioritize these labels in order:

1. `good first issue` — designed for new contributors, usually well-scoped
2. `help wanted` — maintainer explicitly wants outside help
3. `documentation` — docs fixes are low-risk and high-value for non-coders

## GitHub Search Query

```bash
gh issue list \
  --repo "owner/repo" \
  --state open \
  --label "good first issue" \
  --limit 15 \
  --json number,title,labels,updatedAt,comments
```

### Fallback Queries

If no `good first issue` labeled issues exist:

```bash
# Try help wanted
gh issue list --repo "owner/repo" --state open --label "help wanted" --limit 15

# Try documentation
gh issue list --repo "owner/repo" --state open --label "documentation" --limit 15

# Last resort: recently active open issues
gh issue list --repo "owner/repo" --state open --sort updated --limit 20
```

## Issue Selection Criteria

When evaluating issues for a developer-adjacent contributor, check:

- **Clear acceptance criteria** — the issue describes what "done" looks like
- **Maintainer engagement** — recent comments from maintainers showing they're active
- **Scope** — the issue is small enough to fix in one PR (avoid issues that say "refactor the entire X")
- **No assignee** — unassigned issues are more likely to welcome a contribution
- **Reproducibility** — for bugs, the issue has clear reproduction steps

### Red Flags

Skip issues that:
- Have been open for years with no recent activity
- Are labeled `wontfix` or `invalid`
- Require deep architectural knowledge (e.g., "redesign the auth system")
- Are already assigned to someone
