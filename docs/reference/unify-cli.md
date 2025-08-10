# Unify CLI Reference

Complete reference for all Unify command-line options and features.

## Commands

### `unify` (default: build)

Builds your static site from source files.

```bash
unify [options]
# Equivalent to: unify build [options]
```

### `unify build`

Builds your static site for production.

```bash
unify build [options]
```

**Options:**
- `--output-dir <dir>` - Output directory (default: `dist`)
- `--source-dir <dir>` - Source directory (default: `src`)
- `--base-url <url>` - Base URL for absolute links
- `--pretty-urls` - Remove .html extensions from URLs
- `--minify` - Minify HTML output
- `--optimize-images` - Optimize images during build
- `--workers <num>` - Number of parallel workers
- `--verbose` - Show detailed build information
- `--config <file>` - Custom configuration file

**Examples:**
```bash
# Basic build
unify build

# Build with pretty URLs
unify build --pretty-urls

# Build for GitHub Pages
unify build --pretty-urls --base-url https://username.github.io/repo

# Build with optimizations
unify build --minify --optimize-images --workers 4
```

### `unify serve`

Starts the development server with live reload.

```bash
unify serve [options]
```

**Options:**
- `--port <number>` - Port number (default: `3000`)
- `--host <host>` - Host address (default: `localhost`)
- `--dir <directory>` - Directory to serve (default: `src`)
- `--open` - Open browser automatically
- `--cors` - Enable CORS headers
- `--https` - Use HTTPS with self-signed certificate
- `--cert <file>` - HTTPS certificate file
- `--key <file>` - HTTPS key file

**Examples:**
```bash
# Basic development server
unify serve

# Custom port and host
unify serve --port 8080 --host 0.0.0.0

# Open browser automatically
unify serve --open

# Serve with HTTPS
unify serve --https
```

### `unify watch`

Watches for file changes and rebuilds automatically.

```bash
unify watch [options]
```

**Options:**
- `--output-dir <dir>` - Output directory (default: `dist`)
- `--source-dir <dir>` - Source directory (default: `src`)
- `--ignore <pattern>` - Ignore file patterns
- `--delay <ms>` - Debounce delay in milliseconds

**Examples:**
```bash
# Watch and rebuild
unify watch

# Watch with custom delay
unify watch --delay 500

# Watch specific directory
unify watch --source-dir content
```

### `unify init`

Initializes a new Unify project.

```bash
unify init [name] [options]
```

**Options:**
- `--template <name>` - Template to use (default: `basic`)
- `--force` - Overwrite existing files
- `--git` - Initialize Git repository
- `--install` - Install dependencies

**Examples:**
```bash
# Create new project
unify init my-site

# Use specific template
unify init my-blog --template blog

# Initialize with Git
unify init my-site --git
```

### `unify --version`

Shows the current version of Unify.

```bash
unify --version
# or
unify -v
```

### `unify --help`

Shows help information for commands.

```bash
unify --help
unify build --help
unify serve --help
```

## Global Options

These options work with all commands:

- `--config <file>` - Path to configuration file
- `--verbose` - Enable verbose logging
- `--quiet` - Suppress non-error output
- `--no-color` - Disable colored output
- `--debug` - Enable debug logging

## Configuration File

### unify.config.js

Create a configuration file for advanced settings:

```javascript
export default {
  // Source and output directories
  sourceDir: 'src',
  outputDir: 'dist',
  
  // Build options
  build: {
    prettyUrls: true,
    minify: true,
    optimizeImages: true,
    workers: 4,
    baseUrl: process.env.BASE_URL || ''
  },
  
  // Development server options
  serve: {
    port: 3000,
    host: 'localhost',
    open: true,
    cors: true
  },
  
  // File watching options
  watch: {
    ignore: ['node_modules/**', '*.log'],
    delay: 100
  },
  
  // Asset processing
  assets: {
    images: {
      formats: ['webp', 'avif'],
      quality: 85
    },
    css: {
      minify: true,
      autoprefixer: true
    },
    js: {
      minify: true,
      sourceMaps: false
    }
  },
  
  // Template options
  templates: {
    data: {
      siteName: 'My Site',
      author: 'Your Name'
    },
    helpers: {
      formatDate: (date) => new Date(date).toLocaleDateString(),
      capitalize: (str) => str.charAt(0).toUpperCase() + str.slice(1)
    }
  }
};
```

### package.json Scripts

Add convenient npm scripts:

```json
{
  "scripts": {
    "dev": "unify serve",
    "build": "unify build",
    "build:prod": "unify build --minify --optimize-images",
    "preview": "unify serve --dir dist --port 8080",
    "watch": "unify watch"
  }
}
```

## Environment Variables

Unify respects these environment variables:

- `NODE_ENV` - Environment (development/production)
- `PORT` - Development server port
- `BASE_URL` - Base URL for builds
- `UNIFY_SOURCE_DIR` - Source directory
- `UNIFY_OUTPUT_DIR` - Output directory

**Example:**
```bash
# Set environment variables
export NODE_ENV=production
export BASE_URL=https://mysite.com
export PORT=8080

# Run build
unify build
```

## File Patterns

### Include/Exclude Patterns

Use glob patterns to control file processing:

```javascript
// unify.config.js
export default {
  build: {
    include: ['**/*.html', '**/*.md'],
    exclude: ['**/node_modules/**', '**/*.tmp']
  }
};
```

### Asset Patterns

Configure asset handling:

```javascript
export default {
  assets: {
    copy: ['images/**', 'fonts/**'],
    process: ['css/**', 'js/**'],
    ignore: ['**/*.psd', '**/*.ai']
  }
};
```

## SSI Directives

### Built-in Directives

Unify supports these SSI directives:

#### `#include`
```html
<!--#include virtual="/path/to/file.html" -->
<!--#include file="relative/path.html" -->
```

#### `#set`
```html
<!--#set var="title" value="Page Title" -->
<!--#set var="description" value="Page description" -->
```

#### `#echo`
```html
<!--#echo var="title" -->
<!--#echo var="title" default="Default Title" -->
```

#### `#if`
```html
<!--#if expr="${var} = value" -->
  Content when condition is true
<!--#endif -->

<!--#if expr="${var}" -->
  Content when var exists
<!--#else -->
  Content when var doesn't exist
<!--#endif -->
```

#### `#config`
```html
<!--#config timefmt="%Y-%m-%d" -->
<!--#config sizefmt="bytes" -->
```

#### `#exec`
```html
<!--#exec cmd="date" -->
<!--#exec cgi="/cgi-bin/script.cgi" -->
```

### Variables

#### Built-in Variables
- `DOCUMENT_NAME` - Current file name
- `DOCUMENT_URI` - Current file path
- `LAST_MODIFIED` - File modification time
- `DATE_LOCAL` - Current local date/time
- `DATE_GMT` - Current GMT date/time

#### Custom Variables
Set in frontmatter or configuration:

```yaml
---
title: My Page
author: John Doe
tags: [web, development]
---
```

```html
<h1><!--#echo var="title" --></h1>
<p>By <!--#echo var="author" --></p>
```

## Build Process

### File Processing Order

1. **Copy static assets** - Images, fonts, etc.
2. **Process templates** - Resolve includes and variables
3. **Convert Markdown** - Process .md files with frontmatter
4. **Apply layouts** - Use layout templates
5. **Optimize assets** - Minify CSS/JS, optimize images
6. **Generate sitemap** - Create sitemap.xml
7. **Pretty URLs** - Remove .html extensions

### Output Structure

```
dist/
├── index.html
├── about/
│   └── index.html        # pretty URLs
├── blog/
│   ├── index.html
│   └── post-name/
│       └── index.html
├── assets/
│   ├── css/
│   ├── js/
│   └── images/
└── sitemap.xml
```

## Performance

### Build Performance

```bash
# Use multiple workers
unify build --workers 8

# Skip image optimization for faster builds
unify build --no-optimize-images

# Watch mode for development
unify watch
```

### Runtime Performance

```javascript
// unify.config.js - Optimize for performance
export default {
  build: {
    minify: true,
    optimizeImages: true,
    inlineCSS: true,        // Inline critical CSS
    preloadFonts: true,     // Preload web fonts
    generateSW: true        // Generate service worker
  }
};
```

## Debugging

### Verbose Output

```bash
# Show detailed build information
unify build --verbose

# Debug mode with all logs
unify build --debug

# Quiet mode (errors only)
unify build --quiet
```

### Common Issues

#### Include not found
```
Error: Include file not found: /components/header.html
```
**Solution:** Check file path and ensure leading slash for absolute paths.

#### Circular includes
```
Error: Circular include detected: header.html -> footer.html -> header.html
```
**Solution:** Remove circular references between components.

#### Variable not defined
```
Warning: Variable 'title' not defined, using default
```
**Solution:** Set variable with `<!--#set var="title" value="..." -->` or provide default.

## Extensions

### Custom Directives

Create custom SSI directives:

```javascript
// unify.config.js
export default {
  templates: {
    directives: {
      'custom-include': (params, context) => {
        // Custom logic
        return processedContent;
      }
    }
  }
};
```

### Plugins

Add functionality with plugins:

```javascript
// unify.config.js
export default {
  plugins: [
    '@unify/plugin-analytics',
    '@unify/plugin-search',
    ['@unify/plugin-rss', { feedPath: '/feed.xml' }]
  ]
};
```

## Examples

### Basic Website
```bash
unify build --pretty-urls --base-url https://mysite.com
```

### Documentation Site
```bash
unify build --minify --optimize-images --generate-sitemap
```

### Blog
```bash
unify build --pretty-urls --generate-rss --optimize-images
```

### GitHub Pages
```bash
unify build --pretty-urls --base-url https://username.github.io/repo
```

For more examples and detailed guides, see the [Unify documentation](https://github.com/fwdslsh/unify) and [examples repository](https://github.com/fwdslsh/examples).