---
name: write-docs
description: Generate documentation from code with structured analysis and quality gates
user-invocable: true
allowed-tools: Read, Write, Glob, Grep, Edit, Bash
---

# Write Docs

Generate documentation by reading actual code — not by guessing what it does. Every claim traces to a file and line number. Every example is extracted from real usage, not invented.

## Trigger

```
/write-docs
/write-docs src/lib/auth.ts
/write-docs --scope api
```

## Usage

```
/write-docs                          # Document the most-changed files this week
/write-docs src/components/Button.tsx # Document a specific file
/write-docs --scope api              # Document all files in a directory
/write-docs --update                 # Update existing docs with code changes
```

## Workflow

### Step 1: Discover What Needs Documenting

If no target is specified, find the files that need docs most:

1. Run `git log --since="7 days ago" --name-only --pretty=format:""` to find recently changed files
2. For each file, check if a corresponding doc exists (e.g., `src/auth.ts` -> `docs/auth.md`)
3. Rank by: (a) no docs at all, (b) docs older than code, (c) docs exist and are current
4. Present the ranked list and ask the user which to document

### Step 2: Read the Code

For each target file:

1. Read the full file content
2. Identify exported functions, classes, types, and constants
3. For each export, trace its usage: `grep -r "import.*functionName" src/`
4. Note any patterns: error handling conventions, return types, side effects

### Step 3: Generate the Documentation

For each documented item, produce:

- **What it does** — One sentence, derived from the code, not the function name
- **Signature** — The actual TypeScript/JavaScript signature
- **Parameters** — Each parameter with type and what it controls
- **Returns** — What comes back and when it varies
- **Example** — Extracted from actual usage in the codebase, not invented
- **Edge cases** — Error conditions, null returns, thrown exceptions

### Step 4: Quality Gate

Before writing the doc file, verify:

- [ ] Every example compiles (check against actual imports and types)
- [ ] Every claim has a `source: file:line` reference
- [ ] No parameters are described that don't exist in the signature
- [ ] Error cases match actual throw/reject paths in the code

If any check fails, fix it before writing.

### Step 5: Write and Confirm

Write the documentation to the appropriate location:
- API docs: `docs/api/<module>.md`
- Component docs: `docs/components/<component>.md`
- Guide docs: `docs/guides/<topic>.md`

Show the user a summary of what was documented and ask for review.

## The Non-Obvious Part

Most documentation fails because it describes what the code DOES instead of what the developer NEEDS TO KNOW. The function signature already tells you the parameters. The documentation should tell you:

- **When** to use this instead of the alternative
- **Why** the API is shaped this way (the decision, not the description)
- **What breaks** if you use it wrong (the failure mode, not the happy path)

A doc that says "takes a userId string and returns a User object" is useless — the types already say that. A doc that says "call this after authentication but before session hydration — calling it before auth throws UNAUTHORIZED, calling it after hydration wastes a DB round-trip" is invaluable.

Document the WHEN, WHY, and WHAT-BREAKS. The WHAT is already in the code.

## Boundaries

- Does NOT generate docs for external dependencies (only your code)
- Does NOT invent examples — every example must trace to real usage
- Does NOT document internal/private functions unless explicitly asked
- Does NOT modify source code — only reads it and writes doc files
- Does NOT generate JSDoc/TSDoc comments inline — produces standalone doc files

## Output Format

```markdown
# Module Name

> One-sentence description derived from code behavior.

## `functionName(param: Type): ReturnType`

What the developer needs to know — when to use it, why it exists, what breaks.

**Parameters:**
| Name | Type | Description |
|------|------|-------------|
| param | Type | What it controls and valid ranges |

**Returns:** What and when it varies.

**Example:** (from `src/routes/auth.ts:42`)
\`\`\`typescript
// Actual usage from the codebase
const user = await getUser(session.userId);
\`\`\`

**Edge cases:**
- Throws `AuthError` if session expired (see `src/lib/errors.ts:15`)
- Returns `null` if user was soft-deleted (check `user.deletedAt`)

---
*Source: `src/lib/auth.ts` | Last updated: auto-generated*
```

## Error Handling

- If a file has no exports: skip it, note "internal module — no public API"
- If usage search finds zero consumers: flag as "exported but unused — consider removing or documenting the intended use case"
- If the file is too large (>500 lines): document the public API only, suggest splitting
