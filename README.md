# My Construct

> A brief description of what this construct does

Part of the [Loa Constructs](https://constructs.network) ecosystem.

## Getting Started

1. Click **"Use this template"** on GitHub to create your construct repo
2. Update `construct.yaml` with your construct's details
3. Add skills under `skills/` and commands under `commands/`
4. Customize identity files in `identity/`
5. Push to trigger CI validation

## Installation

```bash
constructs-install.sh pack my-construct
```

## Skills

- `example-skill` — Replace with your skill description

## Structure

```
├── construct.yaml          # Pack manifest (required)
├── CLAUDE.md               # Agent instructions
├── identity/
│   ├── persona.yaml        # Cognitive frame and voice
│   └── expertise.yaml      # Domain knowledge boundaries
├── skills/
│   └── example-skill/
│       ├── index.yaml       # Skill metadata + capabilities
│       └── SKILL.md         # Skill instructions
├── commands/
│   └── example-command.md   # Slash command template
├── scripts/
│   └── install.sh           # Post-install hook
└── .github/
    └── workflows/
        └── validate.yml     # CI validation
```

## Development

Validate locally:
```bash
yq eval '.' construct.yaml
```

## License

MIT
