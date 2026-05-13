# Power CAT Copilot CLI Marketplace

A plugin marketplace for [GitHub Copilot CLI](https://docs.github.com/copilot/concepts/agents/about-copilot-cli),
hosting curated plugins from the Power CAT team.

## Add this marketplace

In Copilot CLI:
```bash
/plugin marketplace add https://github.com/ilyafainberg/power-cat-skills-public
/plugin marketplace browse powercat-marketplace
```

## Install a plugin

In Copilot CLI:
```bash
/plugin install writing-styles@powercat-marketplace
```

After installing, restart the CLI (`/restart`).

## Plugins

| Plugin | Description |
|---|---|
| `writing-styles` | Rewrites text as formal business prose. |
| `powercat-check-design` | Evaluates a Power Platform solution design against all 5 pillars of the Power Platform Well-Architected Framework and produces an audience-tailored interactive HTML evaluation artifact. |
| `powercat-roadmap` | Generates a customer-facing PPTX overview of key Microsoft Power Platform and Dynamics 365 roadmap features from a comma-separated product list, emphasizing new previews and GA features for the current release wave. |

## Repository layout

```
.
├── marketplace.json                   # Marketplace index
└── plugins/                           # All Plugins
    └── writing-styles/                # Plugin folder
        ├── plugin.json                # Plugin manifest
        └── skills/                    # Skills included in the plugin
            └── biz/SKILL.md           # Skill prompt
```

## Add another plugin

1. Create a folder under `plugins/<plugin-name>/`.
2. Add a `plugin.json` and any of: `skills/`, `agents/`, `hooks.json`, `.mcp.json`, `lsp.json`.
3. Append an entry to `marketplace.json`.
4. Commit and push. Users run `copilot plugin marketplace update powercat-marketplace`.

See: <https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/plugins-marketplace>
