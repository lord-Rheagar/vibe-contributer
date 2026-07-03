# Vibe Contributer

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

Guides developer-adjacent contributors through the full open-source contribution pipeline — from finding repos and issues through planning, building, critiquing, and shipping PRs — encoding **non-slop process discipline** into every skill.

Built for people who are comfortable with git, CLI tools, and AI agents — but can't read or write code. Inspired by [Matt Van Horn](https://github.com/mvanhorn), who has 100+ merged PRs across Python, Go, OpenCV, uutils/coreutils, and dozens more — without reading code.

## Why?

AI coding agents can write code, but without structured workflow discipline the output is **slop**: vague one-shot prompts, no context feeding, no verification, no iteration. The pattern is *"AI as vending machine"* — accept the first output and push it.

Vibe Contributer encodes the non-slop pattern: *"AI as junior engineer you manage carefully."* Feed it real context. Break asks into verifiable steps. Run the code. Use the agent adversarially against itself. Treat CI and tests as ground truth. Sanity-check diff scope even without reading code line by line.

The bottleneck is repetition — every session, you re-explain the entire workflow to the agent. This plugin encodes that process once, as reusable skills.

## Prerequisites

- [**Claude Code**](https://docs.anthropic.com/en/docs/claude-code) — v1 supports Claude Code only. Cross-platform expansion (Codex, Cursor, Hermes Agent) planned for v2.
- [**GitHub CLI (`gh`)**](https://cli.github.com/) — authenticated. Run `gh auth login` if you haven't.
- **A working local development environment** for the target repo's language (the build skill runs tests and linters).

## Installation

### Option A: Install as a Claude Code Plugin

```bash
# Clone the repo
git clone https://github.com/lord-Rheagar/vibe-contributer.git

# Add to Claude Code's plugin directory
# (follow Claude Code's plugin installation guide for your version)
```

### Option B: Use the skills directly

The skills in `skills/` are standard `SKILL.md` files with YAML frontmatter. You can copy individual skill directories into your existing `.agents/skills/` or equivalent path.

## Skills

| Skill | Purpose | Invocable |
|-------|---------|-----------|
| `search-repo` | Find actively maintained repos with `good first issue` labels, `CONTRIBUTING.md`, and recent commit activity | Standalone |
| `search-issues` | Find workable issues in a selected repo — prioritizes `good first issue`, `help wanted`, `documentation` labels | After `search-repo` |
| `planning-solution` | Read the issue, contributing guidelines, and codebase. Propose a non-coder-readable plan. | After `search-issues` |
| `build` | Implement the plan. Run embedded verification (tests, linters, diff scope, self-critique). | After `planning-solution` |
| `critique` | Multi-stage verification pipeline. Standalone on any diff/PR, or embedded inside `build`. | Standalone or embedded |
| `push-PR` | Verify evidence exists, write a detailed PR description, create the PR, track its status. | After `build` |
| `god-mode` | _Coming in v2_ — full autonomous pipeline with a human confirmation gate before push. | Deferred |

## How It Works

```text
┌─────────────────────────────────────────────────────────────────┐
│                    Vibe Contributer Pipeline                     │
│                                                                 │
│  search-repo ──▶ search-issues ──▶ planning-solution            │
│       │              │                   │                      │
│       ▼              ▼                   ▼                      │
│    repo_url       issue_number        plan_path                 │
│       │              │                   │                      │
│       └──────────────┴───────────────────┘                      │
│                      │                                          │
│                      ▼                                          │
│                    build ──▶ [embedded critique]                │
│                      │              │                           │
│                      ▼              ▼                           │
│              build_state    verification_results                │
│                      │                                      │
│                      ▼                                          │
│                   push-PR ──▶ PR created + tracked             │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

A shared **contribution context** (`.vibe-contributer/context.json`) flows between skills — so you never re-explain context. Each skill reads what it needs and writes what it produces.

### The Non-Slop Verification Pipeline

The `critique` skill (and its embedded version inside `build`) runs a multi-stage verification pipeline:

1. **Local tests** — the primary external ground truth. The agent's own assessment is not ground truth; tests are.
2. **CI results** — when available on an existing PR (via `gh pr checks`).
3. **Diff scope checks** — focused, conventional commit format, matches existing patterns, no unrelated changes.
4. **Structured self-review** — written analysis covering correctness, edge cases, style, and scope.

When no test suite or linter exists, the pipeline degrades to diff-scope + self-critique only (noted in the report).

### The Push Gate

`push-PR` **refuses to push** if no verification evidence exists in the context. This is the non-slop gate — no PR goes out without verification.

## Usage

### Guided Flow (v1)

Run each skill in order, making decisions at each checkpoint:

```bash
# 1. Find a repo to contribute to
#    (invokes the search-repo skill in Claude Code)

# 2. Find an issue to work on
#    (invokes search-issues)

# 3. Plan the fix
#    (invokes planning-solution — produces a plan you can read without knowing code)

# 4. Build the fix with embedded verification
#    (invokes build — runs tests, linters, self-critique automatically)

# 5. (Optional) Run standalone critique on the diff
#    (invokes critique — produces a report you can judge)

# 6. Ship the PR
#    (invokes push-PR — writes the PR description, creates the PR, tracks status)
```

### Standalone Critique

You can invoke `critique` on any branch, diff, or PR — no contribution context required:

```bash
# Critique the current working tree diff
# (invokes critique with no arguments)

# Critique an existing PR
# (invokes critique with a PR number)
```

## Project Structure

```text
vibe-contributer/
├── plugin.json                    # Root manifest
├── .claude-plugin/
│   └── plugin.json                # Claude Code adapter
├── AGENTS.md                      # Canonical agent instructions
├── CLAUDE.md                      # Redirects to AGENTS.md
├── README.md                      # You are here
├── LICENSE                        # MIT
└── skills/
    ├── search-repo/
    │   ├── SKILL.md
    │   └── references/repo-search-filters.md
    ├── search-issues/
    │   ├── SKILL.md
    │   └── references/issue-search-filters.md
    ├── planning-solution/
    │   ├── SKILL.md
    │   └── references/
    │       ├── contributing-guideline-checklist.md
    │       └── plan-template.md
    ├── build/
    │   ├── SKILL.md
    │   └── references/build-verification.md
    ├── critique/
    │   ├── SKILL.md
    │   └── references/verification-pipeline.md
    ├── push-PR/
    │   ├── SKILL.md
    │   └── references/pr-description-template.md
    └── god-mode/
        ├── SKILL.md               # Stub — v2
        └── references/v2-plan.md
```

## Roadmap

- **v1 (current):** 6 functional skills on Claude Code. Guided flow with embedded verification.
- **v2:** God-mode (autonomous pipeline with human confirmation gate before push). Cross-platform adapters for Codex, Cursor, and Hermes Agent. Configurable autonomy levels.

## Acknowledgments

- [Matt Van Horn](https://github.com/mvanhorn) — the inspiration. A non-coder with 100+ merged PRs across major open-source projects, proving that process discipline substitutes for coding ability.
- [Compound Engineering](https://github.com/EveryInc/compound-engineering-plugin) — the structural design reference. Vibe Contributer follows CE's plugin pattern (shared `skills/` directory, thin platform adapters, `AGENTS.md` as canonical instructions).

## License

MIT
