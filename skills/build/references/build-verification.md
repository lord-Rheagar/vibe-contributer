# Build Verification

This reference defines the verification steps the build skill runs after implementing a fix. It is a subset of the full critique verification pipeline (see `skills/critique/references/verification-pipeline.md`).

## Steps (in order)

### 1. Run existing tests

```bash
# Detect the test runner from the project type:
# Python: pytest, python -m pytest
# Node: npm test, yarn test, bun test
# Go: go test ./...
# Ruby: bundle exec rspec, bin/rails test
# Rust: cargo test
```

Record:
- Whether tests passed or failed
- The command that was run
- The last 20 lines of output (for failure context)

### 2. Run linters/formatters (if present)

Check for linting config files:
- `.eslintrc*`, `.prettierrc*` (JS/TS)
- `pyproject.toml` with `[tool.ruff]` or `[tool.black]` (Python)
- `.golangci.yml` (Go)
- `.rubocop.yml` (Ruby)

If found, run the linter. Record pass/fail and output.

### 3. Check diff scope

```bash
git diff --stat
```

Record:
- Number of files changed
- Total lines added/removed
- Whether the changes are focused on the issue's scope

Flag as unreasonable if:
- Files changed > 10 (unless the issue requires it)
- Changes touch files unrelated to the issue
- Lines changed > 500 without a clear reason

### 4. Structured self-critique

Produce a written self-critique covering these dimensions:

- **Correctness:** Does the fix actually solve the issue? Does it handle the described reproduction steps?
- **Edge cases:** What happens with empty input? Null values? Boundary conditions? Concurrent access?
- **Style:** Does the code match existing conventions in the file? Are imports ordered correctly?
- **Scope:** Does the fix do the minimum necessary, or does it include unrelated changes?

For each dimension, note: "No issues found" or describe the issue with severity (critical/minor).

## Degraded Pipeline

When the target repo has no test suite and no linter, the pipeline degrades to:
- Diff scope check (step 3)
- Structured self-critique (step 4)

Note this degradation in the verification results so downstream skills (critique, push-PR) know the evidence is weaker.
