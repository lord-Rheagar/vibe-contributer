# Vibe Contributer

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

AI skills that help you contribute to open source — even if you can't read code.

## What it does

Vibe Contributer turns Claude Code into a structured contribution pipeline. Find a repo, pick an issue, plan a fix, build it, verify it, and ship a PR — all guided by skills that encode engineering process discipline so the output holds up under maintainer review.

You don't need to read code. You need to know how to specify a problem clearly, verify an answer, and iterate based on feedback. This plugin handles the rest: feeding the agent real context, breaking asks into verifiable steps, running tests and linters as ground truth, and checking that the diff is focused before anything gets pushed.

## Who this is for

- **Developer-adjacent contributors** — comfortable with git, CLI tools, and AI agents, but not with reading or writing code
- **Product managers, designers, operators** — people with product instincts who want to contribute to OSS meaningfully
- **First-time open-source contributors** — structured workflow instead of a blank prompt

## Quick start

**Requirements:** [Claude Code](https://docs.anthropic.com/en/docs/claude-code), [Git](https://git-scm.com/), [GitHub CLI (`gh`)](https://cli.github.com/) authenticated

### Install

Open Claude Code and paste this:

> Install Vibe Contributer: run `git clone https://github.com/lord-Rheagar/vibe-contributer.git ~/.claude/skills/vibe-contributer` then add a "Vibe Contributer" section to CLAUDE.md listing the available skills: search-repo, search-issues, planning-solution, build, critique, push-PR.

### Try it

```text
1. search-repo          — "find me a Python repo to contribute to"
2. search-issues        — "show me good first issues in this repo"
3. planning-solution    — "plan a fix for issue #42"
4. build                — "build the fix and run verification"
5. critique             — "critique the diff" (optional, standalone)
6. push-PR              — "push the PR"
```

Each skill reads and writes to a shared context file (`.vibe-contributer/context.json`) so you never re-explain where you are in the pipeline.

## Skills

| Skill | Purpose |
|-------|---------|
| `search-repo` | Search GitHub for actively maintained repos that welcome outside contributions |
| `search-issues` | Find open issues a non-coder could work on with an agent |
| `planning-solution` | Read the issue and codebase, propose a plan you can evaluate without reading code |
| `build` | Implement the plan with embedded verification — tests, linters, diff scope, self-review |
| `critique` | Multi-stage verification pipeline. Standalone on any diff/PR, or embedded in build |
| `push-PR` | Verify evidence exists, write a detailed PR description, create the PR, track its status |
| `god-mode` | _Coming in v2_ — full autonomous pipeline with a human confirmation gate before push |

## How it works

The core loop is six steps: **find** a repo, **pick** an issue, **plan** the fix, **build** with verification, **critique** the result, then **ship** the PR — and each step leaves evidence the next step checks before proceeding.

```text
search-repo → search-issues → planning-solution → build (with embedded critique) → push-PR
```

No PR goes out without verification evidence. The `push-PR` skill checks that tests ran, linters passed, diff scope is reasonable, and a self-critique was produced — and refuses to push if any of that is missing.

### Verification pipeline

Both standalone critique and the embedded version inside `build` run the same four-stage pipeline:

1. **Local tests** — the primary ground truth. The agent's own assessment is not ground truth; tests are.
2. **CI results** — when available on an existing PR (via `gh pr checks`).
3. **Diff scope** — focused, conventional commit format, matches existing patterns, no unrelated changes.
4. **Structured self-review** — written analysis covering correctness, edge cases, style, and scope.

When no test suite or linter exists, the pipeline degrades to diff-scope + self-review only (noted in the report).

## Project structure

```text
vibe-contributer/
├── plugin.json                    # Root manifest
├── .claude-plugin/plugin.json     # Claude Code adapter
├── AGENTS.md                      # Agent instructions
├── CLAUDE.md                      # Redirects to AGENTS.md
├── LICENSE                        # MIT
└── skills/
    ├── search-repo/               # SKILL.md + references/
    ├── search-issues/             # SKILL.md + references/
    ├── planning-solution/         # SKILL.md + references/
    ├── build/                     # SKILL.md + references/
    ├── critique/                  # SKILL.md + references/ (shared verification pipeline)
    ├── push-PR/                   # SKILL.md + references/
    └── god-mode/                  # Stub — v2
```

Each skill is a `SKILL.md` file with YAML frontmatter and a `references/` directory for supporting docs loaded on demand. The plugin follows the structural pattern pioneered by [Compound Engineering](https://github.com/EveryInc/compound-engineering-plugin).

## Roadmap

- **v1 (current):** 6 functional skills on Claude Code. Guided flow with embedded verification.
- **v2:** God-mode (autonomous pipeline with human confirmation gate before push). Cross-platform adapters for Codex, Cursor, and Hermes Agent. Configurable autonomy levels.

## License

MIT
