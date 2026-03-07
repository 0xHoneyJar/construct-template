# Contributing

Thank you for contributing to this construct.

## Structure

```
construct.yaml              # Manifest — skills, commands, metadata
identity/
  persona.yaml              # Cognitive frame and voice
  expertise.yaml            # Domain knowledge and boundaries
  NARRATIVE.md              # Identity narrative (prose, for L2 publishing)
skills/                     # Skill implementations
  <skill-name>/
    index.yaml              # Metadata, triggers, capability routing hints
    SKILL.md                # Instructions and workflow
commands/                   # Slash command files with routing frontmatter
schemas/                    # Validation schemas
contexts/                   # Domain context files (optional)
```

## Adding a Skill

1. Create the skill directory: `skills/my-skill/`

2. Define metadata in `skills/my-skill/index.yaml`:
   ```yaml
   slug: my-skill
   name: "My Skill"

   # Use "Use this skill IF..." for routing clarity
   description: |
     Use this skill IF user invokes `/my-command` OR needs [what it does].
     Produces artifacts to [output path].
   version: 1.0.0

   # Natural language triggers for proactive routing
   # Without these, the skill only works via explicit /command
   triggers:
     - "/my-command"
     - "natural language phrase"
     - "another way users might ask"

   capabilities:
     model_tier: sonnet          # haiku | sonnet | opus
     danger_level: safe          # safe | moderate | high | critical
     effort_hint: small          # small | medium | large
     downgrade_allowed: true
     execution_hint: sequential  # parallel | sequential
     requires:
       native_runtime: false
       tool_calling: true
       thinking_traces: false
       vision: false
   ```

3. Write instructions in `skills/my-skill/SKILL.md`:
   - **Trigger** — how to invoke this skill
   - **Workflow** — step-by-step execution instructions
   - **Boundaries** — what the skill does NOT do

4. Register in `construct.yaml`:
   ```yaml
   skills:
     - slug: my-skill
       path: skills/my-skill
   ```

## Adding a Command

Commands are markdown files in `commands/` with **YAML frontmatter for routing**.

The frontmatter is how the Loa runtime reliably dispatches commands to skills. Without it, the runtime must infer routing from prose — which is non-deterministic and causes inconsistent invocation.

```yaml
---
name: "my-command"
version: "1.0.0"
description: |
  What this command does.
  Routes to my-skill for execution.

arguments:
  - name: "target"
    description: "What to operate on"
    required: false

# REQUIRED: Machine-parseable skill binding
agent: "my-skill"
agent_path: "skills/my-skill"

# Auto-load files into agent context on invocation
# This is how the construct's identity activates
context_files:
  - path: "CLAUDE.md"
    required: true
  - path: "identity/persona.yaml"
    required: true
  - path: "identity/NARRATIVE.md"
    required: false
---

# My Command

You are the **My Construct** agent. Execute the `my-skill` workflow.

## Instructions
...

## Constraints
...
```

### Key frontmatter fields

| Field | Required | Purpose |
|-------|----------|---------|
| `agent` | Yes | Skill slug to route to |
| `agent_path` | Yes | Path to skill directory |
| `context_files` | Recommended | Files to load into agent context (identity, domain knowledge) |
| `arguments` | Optional | Declared parameters |

Register in `construct.yaml`:
```yaml
commands:
  - name: my-command
    path: commands/my-command.md
```

## Adding Domain Context

For constructs with domain-specific knowledge (brand guides, research bases, platform rules), use a `contexts/` or `grimoires/` directory:

```
contexts/
  base/                     # Shared domain knowledge
    domain-rules.md
  overlays/                 # Project-specific overrides
    project-config.yaml
```

Reference these in command frontmatter via `context_files` so they load on invocation.

## Updating Identity

When expanding the construct's scope:

- Add new domains to `identity/expertise.yaml` with honest depth ratings (1-5)
- Update boundaries when the construct's refusal scope changes
- Update persona only if the construct's cognitive approach fundamentally shifts
- Add or update the identity narrative (`identity/*.md`) for publishing readiness

## Validation

CI runs on every push and PR. Validate locally:

```bash
yq eval '.' construct.yaml
yq eval '.' identity/persona.yaml
yq eval '.' identity/expertise.yaml
```

## Guidelines

- **One skill, one responsibility** — keep skills focused and composable
- **Capability metadata on every skill** — `model_tier`, `danger_level`, `effort_hint` enable intelligent routing
- **Triggers on every skill** — without them, the skill is invisible to natural language routing
- **Routing frontmatter on every command** — without `agent`/`agent_path`, invocation is non-deterministic
- **context_files for identity** — without them, the agent executes skills without the construct's persona
- **Document boundaries** — what a skill does NOT do is as important as what it does
- **Be honest about depth** — a depth-3 domain that's accurate is better than a depth-5 that overpromises
- **Write SKILL.md for experts** — specific inputs, outputs, edge cases, not vague descriptions
