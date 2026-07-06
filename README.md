# Vibe Contributer

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

AI skills that help you contribute to open source, even if you can't read code.

## What it does

Vibe Contributer turns your AI coding agent into a structured contribution pipeline. Find a repo, pick an issue, plan a fix, build it, verify it, and ship a PR. All guided by skills that encode engineering process discipline so the output holds up under maintainer review.

You don't need to read code. You need to know how to specify a problem clearly, verify an answer, and iterate based on feedback. This plugin handles the rest: feeding the agent real context, breaking asks into verifiable steps, running tests and linters as ground truth, and checking that the diff is focused before anything gets pushed.

## Who this is for

- **Developer-adjacent contributors** who are comfortable with git, CLI tools, and AI agents, but not with reading or writing code
- **Product managers, designers, operators** with product instincts who want to contribute to OSS meaningfully
- **First-time open-source contributors** who want a structured workflow instead of a blank prompt

## Install

**Requirements:** An AI coding agent (Claude Code, Cursor, Codex, or Hermes Agent), [Git](https://git-scm.com/), [GitHub CLI](https://cli.github.com/) (`gh auth login`)

### Claude Code

Open Claude Code and paste this. Claude does the rest.

> Install Vibe Contributer: run `git clone https://github.com/lord-Rheagar/vibe-contributer.git ~/.claude/skills/vibe-contributer` then add a "Vibe Contributer" section to CLAUDE.md listing the available skills: search-repo, search-issues, planning-solution, build, critique, push-PR. Note that god-mode is coming in v2. Then ask the user if they also want to add Vibe Contributer to the current project so teammates get it.

### Cursor

Clone the repo and add it to your Cursor skills path:

```bash
git clone https://github.com/lord-Rheagar/vibe-contributer.git ~/.cursor/skills/vibe-contributer
```

Or add the `.cursor-plugin` directory as a plugin source in your Cursor settings.

### Codex

```bash
git clone https://github.com/lord-Rheagar/vibe-contributer.git ~/.codex/skills/vibe-contributer
```

The `.codex-plugin/plugin.json` manifest registers the `skills/` directory automatically.

### Hermes Agent

Add the plugin from the marketplace:

```bash
hermes plugin install vibe-contributer
```

Or clone manually:

```bash
git clone https://github.com/lord-Rheagar/vibe-contributer.git ~/.hermes/skills/vibe-contributer
```

The `.agents/plugins/marketplace.json` manifest registers the plugin for Hermes Agent.

### OpenCode

Add to your `opencode.json`:

```json
{
  "plugin": ["vibe-contributer@git+https://github.com/lord-Rheagar/vibe-contributer.git"]
}
```

Restart OpenCode after changing the config.

### Verify GitHub CLI is authenticated

```bash
gh auth status
```

If that fails, run `gh auth login` and follow the prompts. The plugin uses `gh` for repo search, issue reading, PR creation, and PR status tracking.

### Set up the target repo

Vibe Contributer works against a local clone of the repo you want to contribute to. Make sure you have it cloned and can run its test suite:

```bash
git clone https://github.com/owner/repo.git
cd repo
# install dependencies, run tests, etc. per the repo's CONTRIBUTING.md
```

The plugin creates a `.vibe-contributer/context.json` file in the target repo's working directory to track state between skills. Add it to your `.gitignore`:

```bash
echo ".vibe-contributer/" >> .gitignore
```

## Try it

```text
1. search-repo          find a Python repo to contribute to
2. search-issues        show me good first issues in this repo
3. planning-solution    plan a fix for issue #42
4. build                build the fix and run verification
5. critique             critique the diff (optional, standalone)
6. push-PR              push the PR
```

Each skill reads and writes to a shared context file (`.vibe-contributer/context.json`) so you never re-explain where you are in the pipeline.

**After setup, just talk to your agent naturally:**

| You say | What happens |
|---------|-------------|
| "Find me a repo to contribute to" | Runs `search-repo`, presents contributor-friendly repos |
| "Show me good first issues" | Runs `search-issues` on the selected repo |
| "Plan a fix for issue #42" | Runs `planning-solution`, produces a plan you can read without code |
| "Build the fix" | Runs `build` with embedded verification (tests, linters, self-review) |
| "Critique my diff" | Runs `critique` standalone on the current branch |
| "Push the PR" | Runs `push-PR`, verifies evidence exists, creates the PR |

## Skills

| Skill | Purpose |
|-------|---------|
| `search-repo` | Search GitHub for actively maintained repos that welcome outside contributions |
| `search-issues` | Find open issues a non-coder could work on with an agent |
| `planning-solution` | Read the issue and codebase, propose a plan you can evaluate without reading code |
| `build` | Implement the plan with embedded verification: tests, linters, diff scope, self-review |
| `critique` | Multi-stage verification pipeline. Standalone on any diff/PR, or embedded in build |
| `push-PR` | Verify evidence exists, write a detailed PR description, create the PR, track its status |
| `god-mode` | _Coming in v2._ Full autonomous pipeline with a human confirmation gate before push |

## How it works

The core loop is six steps: **find** a repo, **pick** an issue, **plan** the fix, **build** with verification, **critique** the result, then **ship** the PR. Each step leaves evidence the next step checks before proceeding.

```text
search-repo -> search-issues -> planning-solution -> build (with embedded critique) -> push-PR
```

No PR goes out without verification evidence. The `push-PR` skill checks that tests ran, linters passed, diff scope is reasonable, and a self-critique was produced. It refuses to push if any of that is missing.

### Verification pipeline

Both standalone critique and the embedded version inside `build` run the same four-stage pipeline:

1. **Local tests** as the primary ground truth. The agent's own assessment is not ground truth; tests are.
2. **CI results** when available on an existing PR (via `gh pr checks`).
3. **Diff scope** checks: focused, conventional commit format, matches existing patterns, no unrelated changes.
4. **Structured self-review** covering correctness, edge cases, style, and scope.

When no test suite or linter exists, the pipeline degrades to diff-scope + self-review only (noted in the report).

## Project structure

```text
vibe-contributer/
+-- plugin.json                    Root manifest
+-- .claude-plugin/plugin.json     Claude Code adapter
+-- .codex-plugin/plugin.json      Codex adapter
+-- .cursor-plugin/                Cursor adapter (plugin.json + marketplace.json)
+-- .agents/plugins/               Hermes Agent adapter (marketplace.json)
+-- .opencode/                     OpenCode adapter (plugin JS + install guide)
+-- AGENTS.md                      Agent instructions
+-- CLAUDE.md                      Redirects to AGENTS.md
+-- LICENSE                        MIT
+-- skills/
    +-- search-repo/               SKILL.md + references/
    +-- search-issues/             SKILL.md + references/
    +-- planning-solution/         SKILL.md + references/
    +-- build/                     SKILL.md + references/
    +-- critique/                  SKILL.md + references/ (shared verification pipeline)
    +-- push-PR/                   SKILL.md + references/
    +-- god-mode/                  Stub, v2
```

Each skill is a `SKILL.md` file with YAML frontmatter and a `references/` directory for supporting docs loaded on demand. Skills are platform-agnostic. Each platform adapter is a thin metadata wrapper that points to the shared `skills/` directory. The plugin follows the structural pattern pioneered by [Compound Engineering](https://github.com/EveryInc/compound-engineering-plugin).

## Roadmap

- **v1 (current):** 6 functional skills across Claude Code, Cursor, Codex, Hermes Agent, and OpenCode. Guided flow with embedded verification.
- **v2:** God-mode (autonomous pipeline with human confirmation gate before push). Configurable autonomy levels.

## License

MIT
