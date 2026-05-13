# Power CAT Copilot CLI Marketplace

A plugin marketplace for [GitHub Copilot CLI](https://docs.github.com/copilot/concepts/agents/about-copilot-cli),
hosting curated plugins from the Power CAT team.

## Add this marketplace

```bash
copilot plugin marketplace add https://github.com/ilyafainberg/power-cat-skills-public
copilot plugin marketplace browse powercat-marketplace
```

## Install a plugin

```bash
copilot plugin install writing-styles
```

After installing, restart the CLI (`/restart`) or reload skills (`/skills`).

## Plugins

| Plugin | Description |
|---|---|
| `writing-styles` | Rewrites text as formal business prose. |

## Repository layout

```
.
├── .github/plugin/marketplace.json   # Marketplace index (required)
└── plugins/
    └── writing-styles/
        ├── plugin.json               # Plugin manifest
        └── skills/
            ├── biz/SKILL.md
            └── ilya-voice/SKILL.md
```

## Add another plugin

1. Create a folder under `plugins/<plugin-name>/`.
2. Add a `plugin.json` and any of: `skills/`, `agents/`, `hooks.json`, `.mcp.json`, `lsp.json`.
3. Append an entry to `.github/plugin/marketplace.json`.
4. Commit and push. Users run `copilot plugin marketplace update powercat-marketplace`.

See: <https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/plugins-marketplace>
