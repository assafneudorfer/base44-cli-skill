# Project Setup

## Creating a New Project

Use `base44 create` to scaffold a new project from templates:

```bash
# Interactive mode - prompts for template selection
base44 create

# Create with specific name
base44 create my-app

# Create in specific directory
base44 create my-app --path ./projects
```

### Available Templates

When running `base44 create`, you'll be prompted to select a template:

- **Blank** - Minimal setup with basic structure
- **CRM** - Customer relationship management starter
- **E-commerce** - Online store with products and orders
- **Blog** - Content management with posts and categories

### What Gets Created

```
my-app/
├── base44/
│   ├── .app.jsonc          # App ID (auto-generated, gitignored)
│   ├── config.jsonc        # Project configuration
│   ├── entities/           # Entity schema definitions
│   └── functions/          # Serverless functions
├── src/                    # Frontend source code
├── public/                 # Static assets
├── package.json
└── vite.config.js          # Or other build config
```

## Linking an Existing Project

Use `base44 link` to connect a local project to Base44:

```bash
# In your project directory
base44 link
```

This command:
1. Prompts you to select from your Base44 projects
2. Creates `base44/.app.jsonc` with the app ID
3. Downloads existing entities, agents, and config

### Link Options

```bash
# Link to specific app by ID
base44 link --app-id abc123

# Create new Base44 project and link
base44 link --create
```

## Project Initialization Checklist

After creating or linking a project:

1. **Install dependencies**
   ```bash
   npm install
   ```

2. **Install Base44 SDK**
   ```bash
   npm install @base44/sdk
   ```

3. **Configure environment** (if needed)
   ```bash
   # .env.local
   VITE_BASE44_APP_ID=your-app-id
   ```

4. **Start development**
   ```bash
   npm run dev
   ```

## Directory Structure Details

### `base44/` Directory

This is the Base44 configuration directory:

| Path | Purpose |
|------|---------|
| `.app.jsonc` | App ID, not committed to git |
| `config.jsonc` | Project settings, build config |
| `entities/` | Entity JSON schemas |
| `agents/` | AI agent configurations |
| `functions/` | Serverless TypeScript functions |

### Source Code (`src/`)

Your frontend application code. Base44 is framework-agnostic but commonly used with:

- React + Vite
- Next.js
- Vue
- Vanilla JavaScript

## Common Issues

### "Not authenticated"

Run `base44 login` first.

### "Project not linked"

Run `base44 link` to connect to a Base44 project.

### "base44 directory not found"

Ensure you're in the project root or run `base44 create` to scaffold.
