# VS Code Configuration for fwdslsh Development

Get the best development experience with Visual Studio Code when working with fwdslsh tools. This guide covers all extensions, settings, and configurations for optimal productivity.

## Overview

This guide sets up VS Code for:
- **Unify development** with syntax highlighting and IntelliSense
- **Markdown editing** for content creation and documentation
- **Git workflow** integration with fwdslsh tools
- **Docker development** with the fwdslsh Toolkit
- **Live preview** and debugging capabilities

## Quick Setup

### 1. Install Required Extensions

Launch VS Code Quick Open (`Ctrl+Shift+P` / `Cmd+Shift+P`) and install these essential extensions:

```bash
# Core fwdslsh extensions
ext install fwdslsh.unify-vscode

# Essential web development
ext install bradlc.vscode-tailwindcss
ext install ms-vscode.vscode-json
ext install redhat.vscode-yaml

# Markdown and documentation
ext install yzhang.markdown-all-in-one
ext install shd101wyy.markdown-preview-enhanced
ext install davidanson.vscode-markdownlint

# Git and version control
ext install eamodio.gitlens
ext install github.vscode-pull-request-github

# Docker development
ext install ms-vscode-remote.remote-containers
ext install ms-azuretools.vscode-docker
```

### 2. Configure Workspace Settings

Create `.vscode/settings.json` in your project root:

```json
{
  "unify.enable": true,
  "unify.livePreview": true,
  "unify.autoFormat": true,
  "unify.devServer.port": 3000,
  
  "files.associations": {
    "*.unify": "html",
    "*.giv": "yaml"
  },
  
  "emmet.includeLanguages": {
    "unify": "html"
  },
  
  "search.exclude": {
    "**/node_modules": true,
    "**/dist": true,
    "**/.cache": true,
    "**/llms*.txt": true
  },
  
  "files.watcherExclude": {
    "**/node_modules/**": true,
    "**/dist/**": true,
    "**/.cache/**": true
  },
  
  "markdown.preview.linkify": true,
  "markdown.preview.typographer": true,
  
  "gitlens.defaultDateStyle": "relative",
  "gitlens.defaultDateFormat": "YYYY-MM-DD HH:mm",
  
  "terminal.integrated.defaultProfile.linux": "bash",
  "terminal.integrated.profiles.linux": {
    "fwdslsh-toolkit": {
      "path": "docker",
      "args": ["run", "-it", "--rm", "-v", "${workspaceFolder}:/workspace", "fwdslsh/toolkit"]
    }
  }
}
```

### 3. Configure Keybindings

Create `.vscode/keybindings.json`:

```json
[
  {
    "key": "ctrl+shift+u",
    "command": "unify.livePreview.toggle",
    "when": "editorTextFocus"
  },
  {
    "key": "ctrl+shift+b",
    "command": "unify.build",
    "when": "editorTextFocus"
  },
  {
    "key": "ctrl+shift+g",
    "command": "workbench.view.scm",
    "when": "editorTextFocus"
  },
  {
    "key": "ctrl+alt+c",
    "command": "giv.generateCommitMessage",
    "when": "scmResourceGroup"
  }
]
```

## Extension Details

### Unify VS Code Extension

The official Unify extension provides:

#### Features
- **Syntax Highlighting**: HTML templates with Unify-specific syntax
- **IntelliSense**: Auto-completion for components and includes
- **Live Preview**: Real-time preview of your Unify site
- **Error Detection**: Syntax and build error highlighting
- **Snippets**: Quick templates for common patterns

#### Configuration
```json
{
  "unify.devServer.autoStart": true,
  "unify.devServer.port": 3000,
  "unify.livePreview.enabled": true,
  "unify.livePreview.autoRefresh": true,
  "unify.diagnostics.enabled": true,
  "unify.completion.enabled": true,
  "unify.hover.enabled": true
}
```

#### Usage
1. Open any `.html` file in a Unify project
2. Press `Ctrl+Shift+U` to toggle live preview
3. Edit your templates and see changes instantly
4. Use IntelliSense for component references

#### Snippets
- `unify-component`: Create a new component template
- `unify-include`: Add a component include
- `unify-slot`: Define a content slot
- `unify-layout`: Create a layout template

### Essential Extensions for fwdslsh Development

#### Markdown All in One
Perfect for documentation and content creation:

```json
{
  "markdown.extension.toc.levels": "2..6",
  "markdown.extension.toc.unorderedList.marker": "-",
  "markdown.extension.preview.autoShowPreviewToSide": true,
  "markdown.extension.tableFormatter.enabled": true
}
```

#### GitLens
Enhanced Git integration:

```json
{
  "gitlens.codeLens.enabled": true,
  "gitlens.codeLens.authors.enabled": true,
  "gitlens.currentLine.enabled": true,
  "gitlens.hovers.currentLine.over": "line",
  "gitlens.statusBar.enabled": true
}
```

#### Docker Extension
For Toolkit development:

```json
{
  "docker.defaultRegistryPath": "fwdslsh",
  "docker.explorerRefreshInterval": 5000,
  "docker.showStartPage": false
}
```

## Workspace Configuration

### Multi-Root Workspace

For teams working with multiple fwdslsh projects, create a `.code-workspace` file:

```json
{
  "folders": [
    {
      "name": "Main Site",
      "path": "./main-site"
    },
    {
      "name": "Documentation",
      "path": "./docs-site"
    },
    {
      "name": "Blog",
      "path": "./blog"
    }
  ],
  "settings": {
    "unify.workspaceDetection": "auto",
    "search.exclude": {
      "**/node_modules": true,
      "**/dist": true
    }
  },
  "tasks": {
    "version": "2.0.0",
    "tasks": [
      {
        "label": "Build All Sites",
        "type": "shell",
        "command": "docker",
        "args": [
          "run", "--rm", "-v", "${workspaceFolder}:/workspace",
          "fwdslsh/toolkit", "bash", "-c",
          "cd main-site && bun run build && cd ../docs-site && bun run build && cd ../blog && bun run build"
        ],
        "group": "build",
        "presentation": {
          "echo": true,
          "reveal": "always",
          "focus": false,
          "panel": "shared"
        }
      }
    ]
  }
}
```

### Tasks Configuration

Create `.vscode/tasks.json` for common fwdslsh workflows:

```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "Unify: Start Dev Server",
      "type": "shell",
      "command": "bun",
      "args": ["run", "dev"],
      "group": "build",
      "presentation": {
        "echo": true,
        "reveal": "always",
        "focus": false,
        "panel": "shared"
      },
      "isBackground": true,
      "runOptions": {
        "instanceLimit": 1
      }
    },
    {
      "label": "Unify: Build Production",
      "type": "shell",
      "command": "bun",
      "args": ["run", "build"],
      "group": "build",
      "presentation": {
        "echo": true,
        "reveal": "always",
        "focus": false,
        "panel": "shared"
      }
    },
    {
      "label": "Inform: Extract Content",
      "type": "shell",
      "command": "inform",
      "args": ["${input:sourceUrl}", "--output-dir", "extracted"],
      "group": "build"
    },
    {
      "label": "Giv: Generate Commit Message",
      "type": "shell",
      "command": "giv",
      "args": ["message"],
      "group": "build"
    },
    {
      "label": "fwdslsh: Full Workflow",
      "dependsOrder": "sequence",
      "dependsOn": [
        "Inform: Extract Content",
        "Unify: Build Production",
        "Giv: Generate Commit Message"
      ]
    }
  ],
  "inputs": [
    {
      "id": "sourceUrl",
      "description": "URL to extract content from",
      "default": "https://docs.example.com",
      "type": "promptString"
    }
  ]
}
```

## Docker Development Setup

### Dev Container Configuration

Create `.devcontainer/devcontainer.json` for consistent development environments:

```json
{
  "name": "fwdslsh Development",
  "image": "fwdslsh/toolkit:latest",
  "workspaceMount": "source=${localWorkspaceFolder},target=/workspace,type=bind",
  "workspaceFolder": "/workspace",
  "mounts": [
    "source=fwdslsh-cache,target=/root/.cache,type=volume",
    "source=fwdslsh-config,target=/root/.config,type=volume"
  ],
  "forwardPorts": [3000, 8080],
  "portsAttributes": {
    "3000": {
      "label": "Unify Dev Server",
      "onAutoForward": "openBrowser"
    }
  },
  "customizations": {
    "vscode": {
      "extensions": [
        "fwdslsh.unify-vscode",
        "yzhang.markdown-all-in-one",
        "eamodio.gitlens",
        "ms-azuretools.vscode-docker"
      ],
      "settings": {
        "terminal.integrated.defaultProfile.linux": "bash"
      }
    }
  },
  "postCreateCommand": "echo 'fwdslsh development environment ready!'",
  "remoteUser": "root"
}
```

### Docker Compose for Complex Projects

Create `docker-compose.dev.yml`:

```yaml
version: '3.8'

services:
  fwdslsh-dev:
    image: fwdslsh/toolkit:latest
    volumes:
      - .:/workspace:cached
      - fwdslsh-cache:/root/.cache
      - fwdslsh-config:/root/.config
    ports:
      - "3000:3000"
      - "8080:8080"
    environment:
      - OPENAI_API_KEY=${OPENAI_API_KEY}
      - ANTHROPIC_API_KEY=${ANTHROPIC_API_KEY}
    working_dir: /workspace
    command: sleep infinity
    stdin_open: true
    tty: true

volumes:
  fwdslsh-cache:
  fwdslsh-config:
```

## Advanced Configuration

### Custom Snippets

Create `.vscode/snippets.json`:

```json
{
  "Unify Component": {
    "prefix": "unify-component",
    "body": [
      "<!-- ${1:Component Name} -->",
      "<div class=\"${2:component-class}\">",
      "  <slot name=\"${3:default}\">",
      "    ${4:Default content}",
      "  </slot>",
      "</div>"
    ],
    "description": "Create a Unify component template"
  },
  "Unify Include": {
    "prefix": "unify-include",
    "body": [
      "<!-- include ${1:component-name} -->",
      "<div data-include=\"${2:.components/${1:component-name}.html}\">",
      "  ${3:fallback content}",
      "</div>"
    ],
    "description": "Include a Unify component"
  },
  "Giv Config": {
    "prefix": "giv-config",
    "body": [
      "# Giv configuration",
      "api:",
      "  provider: \"${1:openai}\"",
      "  model: \"${2:gpt-4}\"",
      "  temperature: ${3:0.7}",
      "",
      "commit:",
      "  template: |",
      "    {{ type }}({{ scope }}): {{ description }}",
      "    ",
      "    {{ body }}",
      "    ",
      "    {{ footer }}"
    ],
    "description": "Create Giv configuration file"
  }
}
```

### Debugging Configuration

Create `.vscode/launch.json`:

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Debug Unify Build",
      "type": "node",
      "request": "launch",
      "program": "${workspaceFolder}/node_modules/.bin/unify",
      "args": ["build", "--verbose"],
      "console": "integratedTerminal",
      "env": {
        "NODE_ENV": "development"
      }
    },
    {
      "name": "Debug in Docker",
      "type": "node",
      "request": "attach",
      "port": 9229,
      "address": "localhost",
      "localRoot": "${workspaceFolder}",
      "remoteRoot": "/workspace"
    }
  ]
}
```

## Team Configuration

### Shared Settings

Create a `.vscode/settings.json` that the team commits to the repository:

```json
{
  "files.eol": "\n",
  "files.insertFinalNewline": true,
  "files.trimTrailingWhitespace": true,
  "editor.tabSize": 2,
  "editor.insertSpaces": true,
  
  "unify.enable": true,
  "unify.livePreview": true,
  
  "markdown.extension.toc.levels": "2..6",
  "markdownlint.config": {
    "MD013": false,
    "MD041": false
  },
  
  "search.exclude": {
    "**/node_modules": true,
    "**/dist": true,
    "**/.cache": true,
    "**/llms*.txt": true
  }
}
```

### Recommended Extensions List

Create `.vscode/extensions.json`:

```json
{
  "recommendations": [
    "fwdslsh.unify-vscode",
    "yzhang.markdown-all-in-one",
    "davidanson.vscode-markdownlint", 
    "eamodio.gitlens",
    "github.vscode-pull-request-github",
    "ms-vscode-remote.remote-containers",
    "ms-azuretools.vscode-docker",
    "bradlc.vscode-tailwindcss",
    "redhat.vscode-yaml"
  ],
  "unwantedRecommendations": [
    "ms-vscode.vscode-typescript-next"
  ]
}
```

## Productivity Tips

### Quick Actions

1. **Quick File Creation**:
   - `Ctrl+N` → New file
   - Type filename with extension
   - VS Code auto-detects file type and activates appropriate extensions

2. **Component Navigation**:
   - `Ctrl+Click` on include paths to open component files
   - `F12` (Go to Definition) works with Unify includes
   - `Ctrl+Shift+O` to navigate symbols within a file

3. **Live Development**:
   - Keep preview pane open while editing
   - Use `Ctrl+\` to split editor
   - One pane for editing, one for preview

### Workflow Shortcuts

```bash
# Terminal shortcuts within VS Code
Ctrl+` : Toggle integrated terminal
Ctrl+Shift+` : Create new terminal
Ctrl+Shift+P : Command palette

# fwdslsh-specific commands (with extension)
unify:build : Build current project
unify:serve : Start development server
unify:preview : Toggle live preview
giv:message : Generate commit message
```

### Multi-Project Management

1. **Use Workspaces** for related projects
2. **Color-code projects** with Peacock extension
3. **Use Terminal tabs** for different services
4. **Bookmark important files** with Bookmarks extension

## Troubleshooting

### Extension Issues

```bash
# Reload VS Code window
Ctrl+Shift+P → "Developer: Reload Window"

# Reset extension settings
Ctrl+Shift+P → "Preferences: Open Settings (JSON)"
# Remove unify.* settings and restart

# Reinstall Unify extension
Ctrl+Shift+P → "Extensions: Show Installed Extensions"
# Uninstall and reinstall fwdslsh.unify-vscode
```

### Performance Issues

```json
{
  "files.watcherExclude": {
    "**/node_modules/**": true,
    "**/dist/**": true,
    "**/.cache/**": true,
    "**/.git/objects/**": true,
    "**/.git/subtree-cache/**": true,
    "**/llms*.txt": true
  }
}
```

### Docker Integration Problems

```bash
# Ensure Docker is running
docker --version

# Test fwdslsh toolkit
docker run --rm fwdslsh/toolkit:latest which unify

# Check port conflicts
netstat -an | grep 3000
```

## Advanced Features

### Custom Commands

Add to your `settings.json`:

```json
{
  "terminal.integrated.profiles.linux": {
    "fwdslsh Quick Start": {
      "path": "bash",
      "args": ["-c", "docker run -it --rm -v $(pwd):/workspace -p 3000:3000 fwdslsh/toolkit"]
    }
  }
}
```

### Integrated Git Workflow

Configure GitLens for fwdslsh development:

```json
{
  "gitlens.codeLens.authors.enabled": true,
  "gitlens.codeLens.recentChange.enabled": true,
  "gitlens.currentLine.enabled": true,
  "gitlens.statusBar.enabled": true,
  "gitlens.blame.format": "${author}, ${agoOrDate}",
  "gitlens.defaultDateFormat": "YYYY-MM-DD HH:mm"
}
```

## Next Steps

### Learn More
- [Unify Development Guide](unify/development-guide.md)
- [Advanced Git Workflows with Giv](giv/advanced-workflows.md)
- [Team Collaboration Setup](guides/team-collaboration.md)

### Extend Your Setup
- [Custom VS Code Extensions](guides/custom-extensions.md)
- [Automated Workflows](integrations/github-actions.md)
- [Performance Optimization](guides/performance-optimization.md)

### Community
- [Share your VS Code setup](https://github.com/orgs/fwdslsh/discussions)
- [Report VS Code extension issues](https://github.com/fwdslsh/unify-vscode/issues)
- [Contribute improvements](../contributing/contributing.md)

**You're now ready for highly productive fwdslsh development in VS Code!** 🚀

---

*This guide is part of the [fwdslsh examples repository](https://github.com/fwdslsh/examples). For more configuration examples and team setups, explore the [complete documentation](getting-started.md).*