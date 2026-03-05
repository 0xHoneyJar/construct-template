---
name: quick-review
description: Fast code review focused on the most impactful issues
user-invocable: true
allowed-tools: Read, Glob, Grep
---

# Quick Review

Scan changed files and surface the 3-5 issues that matter most. Not a line-by-line audit — a focused pass that catches what would embarrass you in production.

## Trigger

```
/quick-review
```

## Workflow

1. Run `git diff --name-only HEAD~1` to find changed files
2. Read each changed file
3. For each file, check for: security issues (injection, exposed secrets), logic errors (off-by-one, null access, race conditions), and missing error handling at system boundaries
4. Rank findings by blast radius — what affects the most users or causes the worst failure?
5. Present the top 3-5 findings with file, line, and a specific fix

## Boundaries

- Does NOT review style, formatting, or naming conventions
- Does NOT suggest refactors or architectural changes
- Does NOT run tests or linters — this is a human-judgment pass
- Stops at 5 findings — if there are more, say so and recommend a full review

## Output

For each finding:
```
**[severity]** file.ts:42 — What's wrong and why it matters
Fix: specific code change
```
