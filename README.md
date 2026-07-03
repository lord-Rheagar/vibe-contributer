# Vibe Contributer

Guides developer-adjacent contributors through the full open-source contribution pipeline — from finding repos and issues through planning, building, critiquing, and shipping PRs — encoding non-slop process discipline into every skill.

## Philosophy

The slop-PR pattern is "AI as vending machine" — give a vague prompt, accept the first output, push it. The non-slop pattern is "AI as junior engineer you manage carefully" — feed it real context, break asks into verifiable steps, run the code, use the agent adversarially against itself, treat CI and tests as ground truth.

Vibe Contributer encodes the non-slop pattern as structured skills so the workflow is reusable, not re-explained every session.

## Installation

```bash
# Clone the plugin
git clone https://github.com/lord-Rheagar/vibe-contributer.git

# Install in Claude Code
# (follow Claude Code's plugin installation instructions pointing to this directory)
```

## Prerequisites

- `gh` CLI authenticated (`gh auth login`)
- A working local development environment for the target repo's language
- Claude Code (v1 platform; cross-platform expansion in v2)

## Skills

| Skill | Purpose |
|-------|---------|
| `search-repo` | Find actively maintained repos with good first-issue labels |
| `search-issues` | Find workable issues in a selected repo |
| `planning-solution` | Read the issue and codebase, propose a non-coder-readable plan |
| `build` | Implement the plan with embedded verification gates |
| `critique` | Multi-stage verification pipeline (standalone or embedded) |
| `push-PR` | Verify evidence exists, write a detailed PR description, ship |
| `god-mode` | Coming in v2 — full autonomous pipeline |

## Workflow

```text
search-repo → search-issues → planning-solution → build (with embedded critique) → critique (standalone, optional) → push-PR
```

A shared contribution context (`.vibe-contributer/context.json`) flows between skills so you never re-explain context.

## License

MIT
