---
name: base44
description: Complete guide for Base44 CLI and SDK - build full-stack apps with entities, serverless functions, AI agents, and site deployment
version: 1.0.0
tags:
  - base44
  - cli
  - sdk
  - entities
  - serverless
  - ai-agents
  - deployment
globs:
  - "**/base44/**"
  - "**/base44.config.*"
  - "**/.base44*"
---

# Base44 CLI Skill

Build and deploy full-stack applications using the Base44 platform.

## When to Use This Skill

- Creating new Base44 projects
- Defining entity schemas and data models
- Writing serverless functions
- Configuring AI agents
- Deploying sites and applications
- Using the @base44/sdk in frontend code

## Quick Reference

| Command | Description |
|---------|-------------|
| `base44 login` | Authenticate with Base44 |
| `base44 logout` | Logout from current device |
| `base44 whoami` | Display current authenticated user |
| `base44 create [name]` | Create new project from templates |
| `base44 link` | Link local project to Base44 project |
| `base44 deploy` | Deploy all resources |
| `base44 entities push` | Push entity schemas |
| `base44 functions deploy` | Deploy serverless functions |
| `base44 agents push` | Push AI agent configs |
| `base44 agents pull` | Pull agents from Base44 |
| `base44 site deploy` | Deploy built site |
| `base44 dashboard` | Open app dashboard |

## Project Structure

```
project/
├── base44/
│   ├── .app.jsonc          # App ID (not committed)
│   ├── config.jsonc        # Project configuration
│   ├── entities/           # Entity schemas
│   │   └── *.jsonc
│   ├── agents/             # AI agent configurations
│   │   └── *.jsonc
│   └── functions/          # Serverless functions
├── src/                    # Frontend code
└── package.json
```

## Detailed Guides

- @rules/01-project-setup.md - Creating and linking projects
- @rules/02-entities.md - Defining entity schemas
- @rules/03-functions.md - Serverless functions
- @rules/04-agents.md - AI agent configuration
- @rules/05-site-deployment.md - Site deployment
- @rules/06-authentication.md - CLI authentication
- @rules/07-config-files.md - Configuration files
- @rules/08-sdk-integration.md - @base44/sdk usage
