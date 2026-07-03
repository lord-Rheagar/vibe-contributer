# Contributing Guideline Checklist

When reading a repo's CONTRIBUTING.md, extract these key items:

## Build & Test
- How to install dependencies
- How to run the test suite
- How to run linters/formatters
- Any special build steps or environment setup

## Code Style
- Naming conventions (variables, functions, files)
- Import style (absolute vs relative, grouped or not)
- Comment expectations
- Docstring/jSDoc requirements

## Git Workflow
- Branch naming conventions
- Commit message format (conventional commits? specific prefix?)
- Whether squash-merge is expected
- Whether the repo uses develop/main/master as default

## PR Expectations
- PR description format or template
- Whether tests are required for PRs
- Whether the repo has a PR template (`.github/PULL_REQUEST_TEMPLATE.md`)
- Any CI checks that must pass

## Communication
- Whether the repo prefers issues before PRs
- Any discussion channels (Slack, Discord, discussions)
- Response time expectations

## Missing CONTRIBUTING.md

If no contributing guidelines exist, follow general OSS conventions:
- Conventional commit messages (`fix:`, `feat:`, `docs:`, `refactor:`)
- Keep diffs focused and small
- Add tests for new behavior
- Run existing tests before pushing
- Write a clear PR description
