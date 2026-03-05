# Construct Template

> *Build once. Let it propagate.*

Starter template for building **constructs** — installable expertise packages for AI coding agents on the [Constructs Network](https://constructs.network). A construct carries identity, skills, and boundaries. Install one and your agent sees differently. Build one and others can use it.

---

## Level 0: Ship Your First Construct (5 minutes)

```bash
# 1. Create from template
gh repo create my-org/construct-my-expertise \
  --template 0xHoneyJar/construct-template --private --clone
cd construct-my-expertise

# 2. Edit three files
#    construct.yaml  → name, slug, description, author
#    skills/example-simple/SKILL.md  → your skill's instructions
#    CLAUDE.md  → your construct's identity

# 3. Push
git add -A && git commit -m "feat: my first construct" && git push
```

CI validates on push. That's it — you're a construct author.

The example files aren't placeholders. They're a working **Code Review Assistant** construct that catches production issues by blast radius. Read them, then replace them with your expertise.

---

## Level 1: Understand the Structure

```
construct.yaml              # Manifest — name, version, skills, commands
CLAUDE.md                   # Identity — injected when your construct is active
identity/
  persona.yaml              # How it thinks — archetype, cognitive style, voice
  expertise.yaml            # What it knows — domains rated 1-5, hard boundaries
skills/
  example-simple/           # A minimal skill — fast code review pass
    SKILL.md                # Instructions (trigger, workflow, boundaries)
    index.yaml              # Capability metadata (model tier, danger level)
  example-full/             # A complete skill — docs generation with quality gates
    SKILL.md                # Full workflow with 5 steps + quality gate
    index.yaml              # Capability metadata + dependencies
commands/
  example-command.md        # Slash command prompt template
schemas/
  construct.schema.json     # JSON Schema for construct.yaml validation
  persona.schema.yaml       # Validation schema for persona
  expertise.schema.yaml     # Validation schema for expertise
```

### The Two Example Skills

**`example-simple`** — A minimal skill (~30 lines). Shows the essential structure: trigger, workflow, boundaries. If your skill is focused and direct, this is your starting point.

**`example-full`** — A complete skill (~130 lines). Shows every pattern: multi-step workflow, quality gates, the "non-obvious part" section, output format specification, error handling. If your skill is a methodology, start here.

Both skills follow the kishōtenketsu structure:
1. **Introduction** — what this skill is
2. **Development** — the detailed workflow
3. **Twist** — the non-obvious insight that separates good from great
4. **Conclusion** — boundaries and output format

### CLAUDE.md — The Identity Document

This is NOT documentation. It's the instructions injected into the AI runtime when your construct is active. It shapes how the agent *thinks*, not what it *knows*.

The template shows a filled-in example with `<!-- CUSTOMIZE -->` comments marking where to change things:

- **What You See** — the unique perceptual lens (what does your construct notice that others miss?)
- **How You Work** — default behavior when invoked
- **What You Refuse** — hard boundaries that prevent scope creep
- **Your Tools** — skills listed as capabilities, not commands

### Identity Files

**`persona.yaml`** — The cognitive frame. Archetype, thinking style, decision-making methodology, voice. A "Craftsman" archetype obsesses over build quality. A "Researcher" demands evidence before conclusions.

**`expertise.yaml`** — Bounded domains with depth ratings (1-5) and explicit boundaries. Depth 5 = world-class. Depth 2 = awareness. Be honest. Boundaries are features — what your construct refuses to do builds trust.

**Optional: Persona Narrative** — For constructs with strong identity, add a narrative `.md` file in `identity/` (e.g., `ALEXANDER.md`). This is the "power transfer" document — what a developer reads to embody the construct. YAML for machines, narrative for humans.

---

## Level 2: Advanced Features

### Events — Cross-Construct Communication

```yaml
# construct.yaml
events:
  emits:
    - type: forge.my-construct.issue_found
      description: Emitted when the construct finds something
      data_schema:
        severity: string
        file_path: string
  consumes:
    - event: forge.observer.feedback_captured
```

### Pack Dependencies

```yaml
pack_dependencies:
  - slug: observer
    version: ">=1.0.0"
```

### Golden Path — State-Aware Routing

```yaml
golden_path:
  commands:
    - name: "/review"
      truename_map:
        no_changes: "/status"
        changes_staged: "/quick-review"
        pr_open: "/deep-review"
  detect_state: "scripts/detect-state.sh"
```

### Capability Metadata

Every skill's `index.yaml` includes routing hints:

```yaml
capabilities:
  model_tier: sonnet          # haiku | sonnet | opus
  danger_level: moderate      # safe | moderate | high | critical
  effort_hint: medium         # small | medium | large
  execution_hint: sequential  # parallel | sequential
  requires:
    tool_calling: true
    vision: false
```

---

## CI — Graduated Validation

The template ships with 3-level CI that matches your growth as a construct author:

| Level | Runs On | What It Checks |
|-------|---------|----------------|
| **L0: Development** | Every push | YAML valid, required fields present, no placeholder text, skill directories exist, SKILL.md ≥15 lines, CLAUDE.md ≥10 lines |
| **L1: Quality** | PRs to main | Schema validation (ajv), capability metadata enum checks, SKILL.md required sections (Trigger, Workflow, Boundaries) |
| **L2: Publishing** | Releases + manual | quick_start references valid command, identity narrative exists (≥10 lines), zero TODO/FIXME, valid semver |

**L0 blocks placeholder text.** If your `construct.yaml` still says "your-name" or "your-org", CI fails. This is intentional friction — it ensures you've made the construct yours before pushing.

**L2 is the publishing gate.** When you're ready to share your construct with others, trigger L2 manually or cut a release. It verifies everything a consumer would need.

### Security Hardening

- Path traversal protection on all user-controlled paths (identity files, skill paths)
- Placeholder detection covers `repository.url` and `repository.homepage`
- yq pinned to specific version with SHA256 checksum verification
- npm dependencies installed with error visibility (no swallowed failures)

---

## Publishing

Once your construct passes CI:

1. Push to your GitHub repo
2. Register at [constructs.network](https://constructs.network)
3. Others install via `constructs-install.sh pack <your-slug>`

---

## The Key Insight

> *The best construct doesn't help you DO something — it helps you SEE something.*

Artisan doesn't write CSS. Artisan sees that the button feels heavy because the shadow is fighting the border radius. Observer doesn't file issues. Observer sees that users are struggling with the same three-step flow because step 2 assumes context from step 1.

When you build a construct, ask: **what does my expertise see that others miss?** That perceptual shift is the power transfer. Name it. Package it. Let it propagate.

---

## License

MIT — customize the license in `construct.yaml` to match your distribution preferences.

---

<div align="center">

Part of the [Constructs Network](https://constructs.network) · Ridden with [Loa](https://github.com/0xHoneyJar/loa)

</div>
