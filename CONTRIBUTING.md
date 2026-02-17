# Contributing

Thank you for your interest in contributing to this construct!

## Structure

- `construct.yaml` — Pack manifest with skills, commands, and metadata
- `identity/` — Persona and expertise definitions
- `skills/` — Skill implementations (each skill has `index.yaml` + `SKILL.md`)
- `commands/` — Slash command markdown templates

## Adding a Skill

1. Create `skills/my-skill/index.yaml` with metadata and capabilities
2. Create `skills/my-skill/SKILL.md` with instructions
3. Add the skill entry to `construct.yaml` under `skills:`
4. Optionally add a command in `commands/` that invokes the skill

## Validation

Push to trigger CI, or validate locally:

```bash
yq eval '.' construct.yaml
```

## Guidelines

- Keep skills focused — one skill, one responsibility
- Include capability metadata in every `index.yaml`
- Document boundaries in `SKILL.md` (what the skill does NOT do)
- Test with multiple runtimes if possible
