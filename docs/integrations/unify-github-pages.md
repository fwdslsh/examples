# Unify + GitHub Pages Integration

Learn how to deploy your Unify static site to GitHub Pages with automatic builds and deployment using GitHub Actions.

## Overview

GitHub Pages is a free hosting service that's perfect for documentation sites, project pages, and static websites built with Unify. This integration guide covers:

- Setting up automated deployment with GitHub Actions
- Configuring custom domains
- Optimizing builds for performance
- Handling environment-specific configurations

## Prerequisites

- A GitHub repository with your Unify site
- Basic knowledge of Git and GitHub
- GitHub Pages enabled for your repository

## Basic Setup

### 1. Repository Structure

Ensure your repository has this structure:

```
your-repo/
├── .github/
│   └── workflows/
│       └── deploy.yml
├── src/
│   ├── index.html
│   ├── .components/
│   └── assets/
├── package.json
└── README.md
```

### 2. Package.json Configuration

Create or update your `package.json`:

```json
{
  "name": "my-unify-site",
  "version": "1.0.0",
  "scripts": {
    "dev": "unify serve",
    "build": "unify build --pretty-urls",
    "build:gh-pages": "unify build --pretty-urls --base-url https://yourusername.github.io/your-repo-name"
  },
  "devDependencies": {
    "@fwdslsh/unify": "latest"
  }
}
```

### 3. GitHub Actions Workflow

Create `.github/workflows/deploy.yml`:

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Bun
        uses: oven-sh/setup-bun@v1
        with:
          bun-version: latest

      - name: Setup Pages
        uses: actions/configure-pages@v4

      - name: Install dependencies
        run: bun install

      - name: Build with Unify
        run: bun run build:gh-pages

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./dist

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

## Advanced Configuration

### 1. Environment-Specific Builds

Create different build commands for different environments:

```json
{
  "scripts": {
    "dev": "unify serve",
    "build": "unify build --pretty-urls",
    "build:gh-pages": "unify build --pretty-urls --base-url https://yourusername.github.io/your-repo-name",
    "build:custom-domain": "unify build --pretty-urls --base-url https://yourdomain.com",
    "preview": "unify serve --dir dist --port 8080"
  }
}
```

### 2. Conditional Configuration

Use environment variables in your build process:

```yaml
# In .github/workflows/deploy.yml
- name: Build with Unify
  env:
    NODE_ENV: production
    SITE_URL: ${{ github.event.repository.homepage || format('https://{0}.github.io/{1}', github.repository_owner, github.event.repository.name) }}
  run: |
    if [ -n "${{ secrets.CUSTOM_DOMAIN }}" ]; then
      bun run build -- --base-url "https://${{ secrets.CUSTOM_DOMAIN }}"
    else
      bun run build:gh-pages
    fi
```

### 3. Custom Domain Setup

#### Step 1: Configure DNS

Add these DNS records for your custom domain:

```
# For apex domain (example.com)
A    example.com    185.199.108.153
A    example.com    185.199.109.153
A    example.com    185.199.110.153
A    example.com    185.199.111.153

# For subdomain (docs.example.com)
CNAME docs.example.com yourusername.github.io
```

#### Step 2: Add CNAME File

Create `src/CNAME` (Unify will copy it to the output):

```
docs.example.com
```

#### Step 3: Update Build Command

```json
{
  "scripts": {
    "build:production": "unify build --pretty-urls --base-url https://docs.example.com"
  }
}
```

### 4. Build Optimization

#### Cache Dependencies

```yaml
- name: Cache dependencies
  uses: actions/cache@v3
  with:
    path: ~/.bun/install/cache
    key: ${{ runner.os }}-bun-${{ hashFiles('**/bun.lockb') }}
    restore-keys: |
      ${{ runner.os }}-bun-

- name: Install dependencies
  run: bun install --frozen-lockfile
```

#### Optimize Images

```yaml
- name: Optimize images
  run: |
    # Install optimization tools
    bun add -g @squoosh/cli
    
    # Optimize images
    find src/assets/images -name "*.jpg" -o -name "*.png" | xargs -I {} squoosh --webp {} --output-dir dist/assets/images/
```

#### Compress Assets

```yaml
- name: Compress assets
  run: |
    # Gzip files for better compression
    find dist -type f \( -name "*.html" -o -name "*.css" -o -name "*.js" \) -exec gzip -9 -k {} \;
```

## Advanced Features

### 1. Multi-Environment Deployment

Deploy to different environments based on branches:

```yaml
name: Deploy Multi-Environment

on:
  push:
    branches: [main, develop, staging]
  pull_request:
    branches: [main]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Bun
        uses: oven-sh/setup-bun@v1

      - name: Install dependencies
        run: bun install

      - name: Determine environment
        id: env
        run: |
          if [[ "${{ github.ref }}" == "refs/heads/main" ]]; then
            echo "environment=production" >> $GITHUB_OUTPUT
            echo "base_url=https://yourdomain.com" >> $GITHUB_OUTPUT
          elif [[ "${{ github.ref }}" == "refs/heads/staging" ]]; then
            echo "environment=staging" >> $GITHUB_OUTPUT
            echo "base_url=https://staging.yourdomain.com" >> $GITHUB_OUTPUT
          else
            echo "environment=develop" >> $GITHUB_OUTPUT
            echo "base_url=https://dev.yourdomain.com" >> $GITHUB_OUTPUT
          fi

      - name: Build site
        run: unify build --pretty-urls --base-url "${{ steps.env.outputs.base_url }}"

      - name: Deploy to GitHub Pages
        if: steps.env.outputs.environment == 'production'
        uses: actions/deploy-pages@v4
        with:
          artifact_name: github-pages
          
      - name: Deploy to staging
        if: steps.env.outputs.environment == 'staging'
        # Add your staging deployment steps here
        run: echo "Deploy to staging"
```

### 2. Preview Deployments for Pull Requests

```yaml
name: Preview Deployment

on:
  pull_request:
    branches: [main]

jobs:
  preview:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Bun
        uses: oven-sh/setup-bun@v1

      - name: Install dependencies
        run: bun install

      - name: Build preview
        run: unify build --pretty-urls --base-url "https://preview-${{ github.event.number }}.yourdomain.com"

      - name: Deploy preview
        uses: netlify/actions/cli@master
        env:
          NETLIFY_AUTH_TOKEN: ${{ secrets.NETLIFY_AUTH_TOKEN }}
          NETLIFY_SITE_ID: ${{ secrets.NETLIFY_SITE_ID }}
        with:
          args: deploy --dir=dist --alias=preview-${{ github.event.number }}

      - name: Comment PR
        uses: actions/github-script@v6
        with:
          script: |
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: '🚀 Preview deployed to https://preview-${{ github.event.number }}.yourdomain.com'
            })
```

### 3. Automated Content Updates

Pull content from external sources during build:

```yaml
- name: Fetch external content
  run: |
    # Fetch API documentation
    curl -o src/api/openapi.json https://api.yourdomain.com/openapi.json
    
    # Generate API docs
    bun add -g @apidevtools/swagger-parser
    swagger-parser bundle src/api/openapi.json > src/api/api-docs.json

- name: Update changelog
  env:
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
  run: |
    # Generate changelog from GitHub releases
    gh release list --limit 10 --json tagName,name,body > src/data/releases.json
```

## Performance Optimization

### 1. Build Performance

```yaml
# Parallel builds for large sites
- name: Build site (parallel)
  run: |
    # Split source into chunks and build in parallel
    unify build --parallel --workers 4
```

### 2. Asset Optimization

Create an optimization script:

```bash
#!/bin/bash
# optimize-assets.sh

echo "Optimizing images..."
find dist -name "*.jpg" -exec jpegoptim --max=85 {} \;
find dist -name "*.png" -exec optipng -o7 {} \;

echo "Minifying CSS..."
find dist -name "*.css" -exec cleancss -o {} {} \;

echo "Minifying JS..."
find dist -name "*.js" -exec terser {} -o {} \;

echo "Generating WebP images..."
find dist -name "*.jpg" -o -name "*.png" | while read img; do
  cwebp -q 85 "$img" -o "${img%.*}.webp"
done
```

### 3. Caching Strategy

```yaml
- name: Configure caching headers
  run: |
    # Create _headers file for Netlify-style hosting
    cat > dist/_headers << EOF
    /assets/*
      Cache-Control: public, max-age=31536000, immutable
    
    /*.html
      Cache-Control: public, max-age=0, must-revalidate
    
    /sw.js
      Cache-Control: public, max-age=0, must-revalidate
    EOF
```

## Monitoring and Analytics

### 1. Lighthouse CI

```yaml
- name: Run Lighthouse CI
  uses: treosh/lighthouse-ci-action@v9
  with:
    configPath: '.lighthouserc.json'
    uploadArtifacts: true
    temporaryPublicStorage: true
```

Create `.lighthouserc.json`:

```json
{
  "ci": {
    "collect": {
      "url": ["http://localhost:3000"],
      "startServerCommand": "unify serve --port 3000 --dir dist"
    },
    "assert": {
      "assertions": {
        "categories:performance": ["warn", {"minScore": 0.9}],
        "categories:accessibility": ["error", {"minScore": 0.9}],
        "categories:best-practices": ["warn", {"minScore": 0.9}],
        "categories:seo": ["error", {"minScore": 0.9}]
      }
    }
  }
}
```

### 2. Bundle Analysis

```yaml
- name: Analyze bundle
  run: |
    # Generate bundle report
    unify build --analyze
    
    # Upload to bundle analyzer service
    npx bundlesize
```

## Troubleshooting

### Common Issues

#### Build Fails with Path Issues

```yaml
# Fix for path issues in GitHub Actions
- name: Build with correct paths
  run: |
    export NODE_OPTIONS="--max-old-space-size=4096"
    unify build --pretty-urls --base-url "${{ env.SITE_URL }}"
```

#### Large Repository Timeout

```yaml
# For repositories with large history
- name: Checkout with shallow clone
  uses: actions/checkout@v4
  with:
    fetch-depth: 1
```

#### Missing Dependencies

```yaml
# Ensure all dependencies are available
- name: Install system dependencies
  run: |
    sudo apt-get update
    sudo apt-get install -y imagemagick libvips-tools
```

### Debugging

Enable verbose logging:

```yaml
- name: Build with debug info
  run: |
    DEBUG=unify:* unify build --verbose --pretty-urls --base-url "${{ env.SITE_URL }}"
```

Check GitHub Pages status:

```bash
# Check if GitHub Pages is enabled
curl -H "Authorization: token $GITHUB_TOKEN" \
  https://api.github.com/repos/username/repo/pages
```

## Best Practices

### 1. Repository Organization

```
your-repo/
├── .github/
│   ├── workflows/
│   └── ISSUE_TEMPLATE/
├── src/
├── docs/           # Additional documentation
├── scripts/        # Build and deployment scripts
├── .gitignore
├── package.json
└── README.md
```

### 2. Security

```yaml
# Use minimal permissions
permissions:
  contents: read
  pages: write
  id-token: write

# Pin action versions
- uses: actions/checkout@v4.1.1
- uses: oven-sh/setup-bun@v1.2.0
```

### 3. Monitoring

```yaml
# Notify on deployment failure
- name: Notify on failure
  if: failure()
  uses: actions/github-script@v6
  with:
    script: |
      github.rest.issues.create({
        owner: context.repo.owner,
        repo: context.repo.repo,
        title: `Deployment failed for ${context.sha}`,
        body: `Deployment workflow failed. Check the [workflow run](${context.payload.repository.html_url}/actions/runs/${context.runId}) for details.`,
        labels: ['deployment', 'bug']
      })
```

## Example Complete Workflow

Here's a production-ready workflow combining all best practices:

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

env:
  NODE_ENV: production

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4.1.1
        with:
          fetch-depth: 0

      - name: Setup Bun
        uses: oven-sh/setup-bun@v1.2.0
        with:
          bun-version: latest

      - name: Setup Pages
        uses: actions/configure-pages@v4.0.0

      - name: Cache dependencies
        uses: actions/cache@v3
        with:
          path: ~/.bun/install/cache
          key: ${{ runner.os }}-bun-${{ hashFiles('**/bun.lockb') }}

      - name: Install dependencies
        run: bun install --frozen-lockfile

      - name: Build site
        run: |
          bun run build:gh-pages
          
      - name: Optimize assets
        run: |
          # Basic optimization
          find dist -name "*.html" -exec htmlmin {} -o {} \;

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3.0.0
        with:
          path: ./dist

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4.0.0

  lighthouse:
    needs: deploy
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4.1.1

      - name: Run Lighthouse
        uses: treosh/lighthouse-ci-action@v9
        with:
          urls: |
            ${{ needs.deploy.outputs.page_url }}
          uploadArtifacts: true
          temporaryPublicStorage: true
```

## Next Steps

- [Explore more integration examples](../README.md)
- [Learn about Unify advanced features](../unify/user-guide.md)
- [Set up monitoring and analytics](./monitoring.md)
- [Join the community](https://github.com/orgs/fwdslsh/discussions)

Your Unify site is now automatically deployed to GitHub Pages! 🚀