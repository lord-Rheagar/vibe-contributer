---
name: search-issues
description: Find workable issues in a selected repo. Scans open issues for ones a developer-adjacent contributor could meaningfully work on with an AI agent.
argument-hint: "[optional: issue label filter]"
---

# Search Issues

Find open issues in a previously selected repository that a developer-adjacent contributor could meaningfully work on with an AI agent.

## Workflow

1. **Read context.** Read `.vibe-contributer/context.json` to get the `repo_url` and `repo_name`. If the file doesn't exist or `repo_url` is missing, direct the user to run `search-repo` first and stop.

2. **Search issues.** Use `gh issue list` with filters that surface workable issues. See `references/issue-search-filters.md` for the exact query patterns and issue selection criteria.

3. **Present results.** Show the user a curated list of issues with:
   - Issue number and title
   - Labels (highlight `good first issue`, `help wanted`, `documentation`)
   - Last updated date
   - Number of comments (engagement signal)
   - A brief summary of the issue body (first 2-3 sentences)

4. **Let the user choose.** Present the issues and let the user pick one. Ask which issue interests them.

5. **Write context.** After the user selects an issue, update `.vibe-contributer/context.json`:
   ```json
   {"issue_number": 42, "issue_title": "Fix typo in README"}
   ```

6. **Direct to next step.** Suggest the user invoke `planning-solution` to plan a fix for the selected issue.

## Error Handling

- No `repo_url` in context.json: direct user to run `search-repo` first, stop.
- No labeled issues found: fall back to searching open issues with recent activity and clear descriptions. Report that no `good first issue` labeled issues were found.
- Repo has no open issues: report and suggest the user try a different repo via `search-repo`.
