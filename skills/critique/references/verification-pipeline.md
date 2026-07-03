# Verification Pipeline

The shared verification pipeline used by both standalone critique and embedded critique (inside build/god-mode). Both modes load this reference to ensure identical criteria.

## Stages (in order)

### Stage 1: Local Tests (Primary Ground Truth)

Run the project's test suite. This is the primary external verification signal — the agent's own assessment is not ground truth; tests are.

**Detect the test runner:**
- Python: `pytest` or `python -m pytest`
- Node.js: `npm test`, `yarn test`, `bun test`, `pnpm test`
- Go: `go test ./...`
- Ruby: `bundle exec rspec`, `bin/rails test`, `bundle exec minitest`
- Rust: `cargo test`
- Java: `./gradlew test`, `mvn test`
- C/C++: `make test`, `ctest`

**Record:**
- Command run
- Pass/fail status
- Last 20 lines of output (failure context)
- Number of tests passed/failed

**When no test suite exists:** Skip this stage. Note "No test suite found" in the report. The pipeline degrades — diff scope and self-critique become the primary signals, which are weaker.

### Stage 2: CI Results (Secondary Ground Truth)

Only available when critiquing an existing PR. Use `gh pr checks <number> --json name,state,conclusion` to get CI status.

**Record:**
- Each CI check name and state (success/failure/pending)
- Overall CI status

**When not on a PR:** Skip this stage. Embedded critique in build substitutes local test execution (Stage 1) for CI since no PR exists yet. God-mode may push a draft PR to obtain CI results, then re-run critique before converting to ready-for-review.

### Stage 3: Diff Scope Checks

Verify the diff is focused and follows conventions.

```bash
git diff --stat
git diff --name-only
```

**Checks:**
- **File count:** Flag if > 10 files changed (unless the issue requires it)
- **Focus:** Changes should touch only files related to the issue. Flag unrelated changes.
- **Line count:** Flag if > 500 lines changed without a clear reason
- **Conventional commit format:** Check that the branch name or commit messages follow conventional commit format (`fix:`, `feat:`, `docs:`, `refactor:`)
- **Pattern match:** Do the changes follow existing code patterns in the touched files? (e.g., if the file uses tabs, the change uses tabs)

**Record:**
- Files changed (list)
- Lines added/removed
- Whether scope is reasonable (yes/no + why)
- Any convention violations

### Stage 4: Structured Self-Review

Produce a written analysis covering four dimensions. For each, state "No issues found" or describe the issue with severity.

**Correctness:**
- Does the fix actually solve the issue described?
- Does it handle the reproduction steps from the issue?
- Are there any logical errors in the implementation?

**Edge cases:**
- What happens with empty input?
- What happens with null/nil/undefined values?
- Are boundary conditions handled?
- Could concurrent access cause problems?

**Style:**
- Does the code match existing conventions in the file?
- Are imports ordered correctly?
- Are variable/function names consistent with the codebase?
- Is there any dead code or unused imports?

**Scope:**
- Does the fix do the minimum necessary to solve the issue?
- Are there any unrelated changes that should be removed?
- Is the fix over-engineered for the problem?

**Severity levels:**
- `critical` — must fix before pushing (broken functionality, wrong fix, tests fail)
- `minor` — worth noting but not blocking (style nits, minor edge cases)

## Overall Verdict

- **PASS:** All stages pass (or skipped stages are acceptable). No critical findings.
- **FAIL:** Any stage fails, or any critical finding is unresolved.
- **NEEDS_ATTENTION:** All stages pass but minor findings exist that the user should review.

## Report Format

```markdown
## Critique Report

**Verdict:** PASS / FAIL / NEEDS_ATTENTION

### Tests
- Status: PASS / FAIL / SKIPPED
- Command: `pytest`
- Output: (last 20 lines)

### CI
- Status: PASS / FAIL / SKIPPED
- Checks: (list)

### Diff Scope
- Files changed: 2
- Lines: +15 / -3
- Scope reasonable: YES
- Conventions followed: YES

### Self-Review
- Correctness: No issues found
- Edge cases: Minor — empty input not handled, but unlikely in practice
- Style: No issues found
- Scope: No issues found

### Critical Findings
(none)

### Minor Findings
- Empty input edge case in validate.ts — consider adding a null check

### Summary
The fix looks good. Tests pass. The diff is small and focused (2 files). One minor thing to watch: the empty input case isn't handled, but it's unlikely to occur in practice.
```
