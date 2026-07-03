---
name: search-repo
description: Find actively maintained open-source repos worth contributing to. Searches GitHub for contributor-friendly repos with good first-issue labels, active maintenance, and contributing guidelines.
argument-hint: "[optional: language or topic filter]"
---

# Search Repos

Find actively maintained open-source repositories that accept outside contributions and are good targets for a developer-adjacent contributor working with an AI agent.

## Workflow

1. **Check prerequisites.** Verify `gh` CLI is authenticated by running `gh auth status`. If it fails, direct the user to run `gh auth login` and stop.

2. **Search GitHub.** Use `gh search repos` with filters that surface contributor-friendly repos. See `references/repo-search-filters.md` for the exact query patterns and contributor-friendliness signals.

3. **Present results.** Show the user a curated list of repos with:
   - Repo name and URL
   - Star count (popularity signal)
   - Primary language
   - Whether it has `good first issue` label
   - Whether it has `CONTRIBUTING.md`
   - Last commit date (activity signal)
   - Open issue count

4. **Let the user choose.** Present the repos and let the user pick one. Ask one question at a time — first ask if any of the results interest them, then narrow.

5. **Write context.** After the user selects a repo, write the repo URL and name to `.vibe-contributer/context.json` in the current working directory:
   ```json
   {"repo_url": "https://github.com/owner/repo", "repo_name": "owner/repo"}
   ```
   If the file doesn't exist, create it. If it exists, update the `repo_url` and `repo_name` fields.

6. **Direct to next step.** Suggest the user invoke `search-issues` to find workable issues in the selected repo.

## Error Handling

- `gh` not authenticated: report the error, direct user to `gh auth login`, stop.
- No results match filters: widen the search (remove the `good first issue` filter, increase the star threshold, or remove language filter). If still no results, report and ask the user to try different keywords.
- Rate limited: wait and retry, or suggest the user narrow their search terms.
