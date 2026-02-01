# Site Deployment

Deploy your frontend application to Base44's hosting platform.

## Overview

Base44 can host your built frontend assets (HTML, CSS, JS) with:
- Global CDN distribution
- Automatic HTTPS
- Custom domain support
- Environment-based deployments

## Configuration

Configure site settings in `base44/config.jsonc`:

```jsonc
{
  "site": {
    // Build output directory
    "buildDir": "dist",

    // Build command
    "buildCommand": "npm run build",

    // Public path / base URL
    "publicPath": "/",

    // Environment variables for build
    "env": {
      "VITE_API_URL": "https://api.base44.com"
    }
  }
}
```

## Build Commands

Common build configurations:

### Vite (React/Vue)

```jsonc
{
  "site": {
    "buildDir": "dist",
    "buildCommand": "npm run build"
  }
}
```

### Next.js (Static Export)

```jsonc
{
  "site": {
    "buildDir": "out",
    "buildCommand": "npm run build && npm run export"
  }
}
```

### Create React App

```jsonc
{
  "site": {
    "buildDir": "build",
    "buildCommand": "npm run build"
  }
}
```

## Deploying

### Manual Deployment

```bash
# Build and deploy site
base44 site deploy

# Deploy with verbose output
base44 site deploy --verbose
```

### What Happens

1. Runs `buildCommand` (if configured)
2. Uploads contents of `buildDir`
3. Invalidates CDN cache
4. Site goes live at your Base44 URL

### Deploy Pre-built

If you've already built:

```bash
# Skip build, just upload
base44 site deploy --skip-build
```

## Full Deploy (Everything)

Deploy entities, functions, agents, AND site:

```bash
# Deploy all resources
base44 deploy

# Equivalent to:
base44 entities push
base44 functions deploy
base44 agents push
base44 site deploy
```

## Site URL

After deployment, your site is available at:

```
https://{app-name}.base44.app
```

Or with a custom domain if configured.

## Custom Domains

Configure custom domains in the Base44 dashboard:

1. Go to **Settings > Domains**
2. Add your domain
3. Configure DNS records as shown
4. SSL certificate auto-provisioned

## Environment Variables

### Build-time Variables

Set in `config.jsonc`:

```jsonc
{
  "site": {
    "env": {
      "VITE_APP_NAME": "My App",
      "VITE_API_ENDPOINT": "https://api.example.com"
    }
  }
}
```

### Runtime Variables

For values that shouldn't be in code, use the dashboard:

1. Go to **Settings > Environment**
2. Add variables
3. Access via your framework's env handling

## SPA Routing

For single-page apps with client-side routing, Base44 automatically handles:

- Serving `index.html` for all routes
- Proper 404 handling
- History API support

No additional configuration needed.

## Deployment Workflow

### Development

```bash
# Run local dev server
npm run dev

# Test with Base44 backend
VITE_BASE44_APP_ID=your-app npm run dev
```

### Staging

```bash
# Deploy to staging environment
base44 site deploy --env staging
```

### Production

```bash
# Deploy to production
base44 site deploy --env production
```

## Rollbacks

If a deployment causes issues:

```bash
# List recent deployments
base44 site deployments

# Rollback to previous
base44 site rollback

# Rollback to specific version
base44 site rollback --version abc123
```

## Build Optimization

### Vite Configuration

```javascript
// vite.config.js
import { defineConfig } from 'vite';

export default defineConfig({
  build: {
    // Optimize chunk size
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ['react', 'react-dom'],
          base44: ['@base44/sdk']
        }
      }
    },
    // Enable compression
    minify: 'terser',
    // Source maps for debugging
    sourcemap: true
  }
});
```

### Asset Optimization

- Images: Use WebP/AVIF formats
- Fonts: Subset and preload
- CSS: Purge unused styles
- JS: Code split by route

## Troubleshooting

### Build Fails

```bash
# Check build locally first
npm run build

# View verbose build output
base44 site deploy --verbose
```

### 404 on Routes

Ensure SPA fallback is working:

```jsonc
// config.jsonc
{
  "site": {
    "spa": true  // Enable SPA mode
  }
}
```

### Assets Not Loading

Check `publicPath` matches your setup:

```jsonc
{
  "site": {
    "publicPath": "/"  // or "/app/" for subdirectory
  }
}
```

### Slow Deployments

- Reduce build output size
- Use `.base44ignore` to exclude files
- Enable incremental uploads

## .base44ignore

Exclude files from deployment:

```
# .base44ignore
node_modules/
*.map
*.log
.env*
coverage/
```
