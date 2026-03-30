# Code Review Skill

## Overview
`code-review` is a skill for reviewing code quality. It evaluates code systematically with a focus on safety, correctness, and balanced improvements across performance and readability.

Key review dimensions include:
- Security (Critical)
- Correctness (Critical)
- Performance (Important)
- Readability (Important)
- Nothing UI component consistency (Android UI ecosystem review)
- SOLID + architecture smells (Consider)
- Removal candidates + iteration plan (Consider)
- Test coverage (Consider)

## When to Use
Use this skill when:
- The user explicitly requests code review (e.g., “review this”, “check my code”, “what’s wrong with this code”).
- The user provides a snippet, diff, or PR and wants feedback on code quality.
- You need to assess security risks, correctness issues, performance hotspots, or maintainability concerns.

## Review Process
The review process is:

1. **Understand the context** — What is the code trying to do? What language/framework is it using? Is it a snippet, a full file, or a diff/PR?
2. **Read through completely first** — Don’t start commenting immediately. Understand the overall structure and intent before diving into details.
3. **Review systematically** — Check focus areas in priority order (Security/Correctness first, then performance/readability, etc.).

## Focus Areas (Priority Order)

### Security (Critical)
Check for common security vulnerabilities and gaps, including:
- XSS, injection (SQL/NoSQL/command), SSRF, path traversal
- AuthZ/AuthN gaps, missing tenancy checks
- Secret leakage (logs/env/files)
- Rate limits, unbounded loops, CPU/memory hotspots
- Unsafe deserialization, weak crypto, insecure defaults
- **Race conditions**: concurrent access, check-then-act, TOCTOU, missing locks

When reporting, clarify both:
- **Exploitability** (how an attacker could trigger it)
- **Impact** (what damage can occur)

### Correctness (Critical)
Look for logic bugs and failure points:
- Error handling: swallowed exceptions, overly broad catch blocks, missing propagation, async errors
- Boundary conditions: null/undefined handling, empty collections, numeric boundaries, off-by-one
- Concurrency/atomicity issues and race conditions

### Performance (Important)
Identify performance risks such as:
- Inefficient algorithms or high complexity on hot paths
- Unnecessary allocations/copies
- CPU/memory hotspots

### Readability (Important)
Assess maintainability concerns:
- Unclear naming
- Functions doing too many things
- Deep nesting that could be simplified
- Missing or misleading comments
- Inconsistent style

### Nothing UI Component Consistency
If the review involves Android ecosystem UI components, ensure consistency with Nothing UI support components:
- If a NothingUISupport component is expected, call out mismatches and suggest alternatives.
- Otherwise, skip this section.

### SOLID + Architecture Smells (Consider)
Look for design and architecture issues:
- SRP: overloaded responsibilities
- OCP: behavior added by frequent edits instead of extension points
- LSP: subclasses breaking expectations / requiring type checks
- ISP: interfaces too broad with unused members
- DIP: high-level logic too tightly coupled to low-level implementations

If you propose refactoring, explain:
- *Why* it improves cohesion/coupling
- An incremental, minimal, safe plan (avoid large rewrites unless necessary)

### Removal Candidates + Iteration Plan (Consider)
Evaluate potential cleanup opportunities:
- Identify unused/redundant code or feature-flagged-off parts
- Distinguish:
  - safe delete now
  - defer with a concrete plan
- Provide a follow-up plan with steps and checkpoints (tests/metrics)

### Test Coverage (Consider)
Review whether critical paths are tested:
- Edge cases and extreme inputs
- Clarity and maintainability of tests
- Missing scenarios that would prevent regressions

## Output Format
Structure the review output as follows:

### Summary
A 2–3 sentence overview of code quality and the most important findings.

### Issues Found
For each issue, use this template:

- **[SEVERITY] Category: Brief title**
  - **Location:** `filename:line` or a clear description of where
  - **Problem:** What’s wrong and why it matters
  - **Suggestion:** How to fix it (include a code example if helpful)

Severity levels:
- **CRITICAL** — security vulnerabilities, bugs causing failures, data loss risks
- **WARNING** — performance problems, potential bugs, maintainability concerns
- **SUGGESTION** — style improvements, minor optimizations, nice-to-haves

### What’s Good
Briefly note what was done well.

### Recommended Actions
A prioritized list of what to fix first, grouped by urgency.

### Output File
After completing the review, **always save the full review result to a Markdown file**:
- If a commit ID is available: `review-<commitId>.md`
- If a patch name or keyword is available: `review-<commitId>-<patchKeyword>.md`
- If neither is available: `code-review-result.md`

The file must contain the complete review output in Markdown.

## Review Guidelines
- **Be specific** — explain what’s wrong, why it matters, and how to fix it (code snippets when helpful).
- **Be constructive** — frame feedback as improvement, not failure.
- **Calibrate severity honestly** — reserve CRITICAL for genuinely urgent issues.
- **Consider the context** — prototypes can have different standards than production.
- **Acknowledge uncertainty** — if something can’t be verified, state what you’d want to confirm.

## Handling Different Inputs
- **Code snippets:** review provided content; request more context if needed.
- **Full files:** review comprehensively, but focus on the most important issues.
- **Diffs/PRs:** focus on changed lines while considering interactions with surrounding code.

## Language-Specific Considerations
- JavaScript/TypeScript: type safety, async/await handling, dependency security
- Python: idiomatic patterns, type hints, proper exception handling
- Go: error handling, goroutine safety, interface usage
- Rust: avoid unwrap abuse, ensure proper error propagation
- SQL: injection risks, query efficiency, indexing hints
- Others: apply relevant idioms and best practices

## Example Usage
Request examples:
- “Review this code for security and performance.”
- “What’s wrong with this implementation? Suggest fixes.”

The output will be structured to highlight the most important findings and include an archive-ready Markdown result file.
