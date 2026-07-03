# Plan Template

Use this structure when writing a plan document. The plan must be readable by someone who cannot read code.

## Plan: [Issue Title]

**Issue:** #[number] — [issue title]
**Repo:** [owner/repo]

### What the fix does

Describe in 2-3 sentences what the fix does in functional terms. No code, no jargon. Example: "When a user types an invalid email, the app currently crashes. This fix adds a check that shows a friendly error message instead."

### Why this approach

Explain why this approach was chosen over alternatives. 1-2 sentences. Example: "Adding validation at the input layer is simpler than rewriting the form submission handler, and it catches the error earlier."

### Files touched

List each file that will be modified and why:
- `src/components/Form.tsx` — add email validation before submission
- `src/utils/validate.ts` — new helper function for email checking

### Verification plan

Describe how the fix will be verified:
- Run the existing test suite to ensure nothing breaks
- Run the linter to ensure code style matches
- Check that the diff only touches the files listed above
- Self-critique: does this handle edge cases? (empty input, null, unicode emails)

### Trade-offs

Note any trade-offs or areas the maintainer should review closely:
- "This doesn't handle unicode emails — the maintainer may want to add full RFC 5322 validation"
- "This changes the error message format — the maintainer should verify it matches their style"
