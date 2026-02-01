# Base44 CLI Skill

A comprehensive Claude Code plugin for the Base44 CLI (v0.0.26) covering all CLI commands and SDK integration patterns.

## Quickstart

### Add the Marketplace

```bash
/plugin marketplace add github:assafneudorfer/base44-cli-skill
```

### Install the Plugin

```bash
/plugin install base44-skill@base44-marketplace
```

That's it! The skill is now available in your Claude Code sessions.

### Local Installation (for development)

```bash
git clone https://github.com/assafneudorfer/base44-cli-skill.git ~/base44-cli-skill
/plugin marketplace add ~/base44-cli-skill
/plugin install base44-skill@base44-marketplace
```

Or test the plugin directly without marketplace:

```bash
claude --plugin-dir ~/base44-skill
```

## Prerequisites

Install the Base44 CLI:

```bash
npm install -g base44
```

## What's Included

The `base44-skill` plugin provides comprehensive coverage of:

- **CLI Commands**: All base44 commands (create, link, deploy, entities, functions, agents, site)
- **Project Structure**: Understanding base44/ directory layout
- **Entity Schemas**: JSON Schema-based entity definitions
- **Serverless Functions**: TypeScript functions with full typing
- **AI Agents**: Agent configuration and tool permissions
- **Site Deployment**: Frontend build and deployment
- **SDK Integration**: @base44/sdk patterns for frontend code

## Usage

Once installed, Claude Code will automatically use the Base44 skill when:
- Working in projects with `base44/` directories
- Creating or modifying Base44 configuration files
- Working with `@base44/sdk`

## Quick Start with Base44

```bash
# Create a new project
base44 create my-app

# Or link an existing project
base44 link

# Deploy everything
base44 deploy
```

## Documentation

See `skills/base44/rules/` for detailed guides:

| Guide | Description |
|-------|-------------|
| `01-project-setup.md` | Creating and linking projects |
| `02-entities.md` | Entity schema definitions |
| `03-functions.md` | Serverless function development |
| `04-agents.md` | AI agent configuration |
| `05-site-deployment.md` | Site build and deployment |
| `06-authentication.md` | CLI authentication flow |
| `07-config-files.md` | Configuration file reference |
| `08-sdk-integration.md` | @base44/sdk usage patterns |

## Plugin Structure

```
base44-cli/
├── .claude-plugin/
│   ├── plugin.json               # Plugin manifest
│   └── marketplace.json          # Marketplace catalog
├── skills/
│   └── base44/
│       ├── SKILL.md              # Main skill definition
│       └── rules/                # Detailed guides
│           ├── 01-project-setup.md
│           ├── 02-entities.md
│           ├── 03-functions.md
│           ├── 04-agents.md
│           ├── 05-site-deployment.md
│           ├── 06-authentication.md
│           ├── 07-config-files.md
│           └── 08-sdk-integration.md
├── README.md
├── LICENSE
└── .gitignore
```

## Updating the Plugin

To get the latest version:

```bash
/plugin marketplace update base44-marketplace
/plugin update base44-skill@base44-marketplace
```

## License

MIT
