# What is Lift and How It Works

Lift is a specialized tool in the fwdslsh ecosystem that prepares documentation for AI consumption by analyzing project files and generating structured content summaries. It's designed to bridge the gap between human-readable documentation and AI-friendly formats.

## What Lift Does

Lift scans your project directory and creates two key outputs:
- **`llms.txt`** - A structured summary perfect for AI context windows
- **`llms-full.txt`** - Complete content for comprehensive AI training

Think of Lift as the "AI preparation" step in your documentation pipeline.

## How Lift Works

### 1. File Discovery Algorithm

Lift uses an intelligent file discovery system that:

**Prioritizes important files:**
- README files (README.md, README.txt, etc.)
- Documentation files (*.md, *.rst, *.txt)
- License files (LICENSE, COPYING, etc.)
- Configuration files (package.json, setup.py, etc.)

**Ignores common noise:**
- Binary files and images
- Build artifacts (dist/, build/, node_modules/)
- Version control directories (.git/, .svn/)
- OS-specific files (.DS_Store, Thumbs.db)

**Follows hierarchy:**
1. Root-level documentation
2. docs/ directory contents
3. Source code comments and docstrings
4. Configuration and metadata files

### 2. Content Processing

For each discovered file, Lift:

1. **Extracts key information:**
   - File path and type
   - Content summary
   - Key concepts and topics
   - Structural elements (headings, code blocks, etc.)

2. **Applies smart filtering:**
   - Removes redundant boilerplate
   - Preserves essential context
   - Maintains logical file relationships
   - Optimizes for AI consumption

3. **Structures output:**
   - Logical grouping by importance
   - Clear section boundaries
   - Hierarchical organization
   - Consistent formatting

### 3. Output Generation

**llms.txt (Concise):**
- Perfect for AI chat contexts (typically under 8K tokens)
- Includes essential project information
- Summarizes key concepts and structure
- Provides enough context for informed AI responses

**llms-full.txt (Comprehensive):**
- Complete project documentation in AI-friendly format
- Suitable for training or fine-tuning
- Includes all relevant content with minimal filtering
- Maintains full context and relationships

## Installation and Basic Usage

### Installation

```bash
# Via npm (requires Node.js/Bun)
npm install -g @fwdslsh/lift

# Via the fwdslsh Toolkit (recommended)
docker run -it fwdslsh/toolkit lift --version

# Direct binary download
curl -fsSL https://raw.githubusercontent.com/fwdslsh/lift/main/install.sh | sh
```

### Basic Usage

```bash
# Generate AI-friendly documentation for current directory
lift

# Specify input and output directories
lift --input ./docs --output ./ai-ready

# Generate only the concise summary
lift --summary-only

# Silent mode (no progress output)
lift --silent
```

## Practical Examples

### Example 1: Basic Project Documentation

```bash
# Your project structure:
my-project/
├── README.md
├── docs/
│   ├── installation.md
│   ├── usage.md
│   └── api.md
├── src/
│   └── index.js
└── package.json

# Run Lift
cd my-project
lift

# Generated files:
├── llms.txt          # Concise summary (~2-4K tokens)
└── llms-full.txt     # Complete content (~10-20K tokens)
```

**llms.txt output structure:**
```
# My Project - AI Documentation Summary

## Project Overview
[Extracted from README.md and package.json]

## Key Documentation
### Installation
[Summary from docs/installation.md]

### Usage Guide
[Key points from docs/usage.md]

### API Reference
[Essential API information from docs/api.md]

## Project Structure
[Intelligent analysis of src/ directory]
```

### Example 2: Documentation Website

```bash
# Documentation site structure:
docs-site/
├── README.md
├── content/
│   ├── guides/
│   │   ├── getting-started.md
│   │   ├── advanced-usage.md
│   │   └── troubleshooting.md
│   ├── api/
│   │   ├── authentication.md
│   │   └── endpoints.md
│   └── examples/
│       ├── basic.md
│       └── advanced.md
└── config.yaml

# Generate AI documentation
lift --input content --output ai-docs

# Use specific output format
lift --format markdown --include-structure
```

### Example 3: Code Project with Documentation

```bash
# Software project:
my-app/
├── README.md
├── CHANGELOG.md
├── docs/
│   └── architecture.md
├── src/
│   ├── main.py
│   ├── utils.py
│   └── __init__.py
├── tests/
│   └── test_main.py
└── requirements.txt

# Generate with code analysis
lift --include-code --max-file-size 50KB

# Focus on documentation only
lift --docs-only --exclude-patterns "*.py,*.js"
```

## Configuration Options

### Command Line Options

```bash
# Input/Output Control
lift --input ./src-docs --output ./ai-ready
lift --input-dir docs --output-dir generated

# Content Control
lift --max-file-size 100KB    # Skip large files
lift --max-files 500          # Limit number of files processed
lift --include-code           # Include source code analysis
lift --docs-only              # Documentation files only

# Output Format
lift --format markdown        # Output format (markdown, txt, json)
lift --summary-only           # Generate only llms.txt
lift --full-only              # Generate only llms-full.txt
lift --combine                # Single combined output file

# Processing Options
lift --silent                 # No progress output
lift --verbose                # Detailed processing logs
lift --dry-run               # Show what would be processed

# Filtering Options
lift --include-patterns "*.md,*.rst"  # Include specific patterns
lift --exclude-patterns "*.log,*.tmp" # Exclude specific patterns
lift --min-file-size 100B            # Skip tiny files
```

### Configuration File

Create `.lift.yaml` in your project root:

```yaml
# .lift.yaml
input:
  directory: "."
  max_file_size: "100KB"
  max_files: 1000
  
output:
  directory: "./ai-docs"
  format: "markdown"
  generate_summary: true
  generate_full: true
  
processing:
  include_code: false
  docs_only: true
  preserve_structure: true
  
filters:
  include_patterns:
    - "*.md"
    - "*.rst"
    - "*.txt"
    - "README*"
    - "CHANGELOG*"
  
  exclude_patterns:
    - "node_modules/**"
    - "build/**"
    - "dist/**"
    - "*.log"
    - "*.tmp"
  
  exclude_directories:
    - ".git"
    - ".svn"
    - "__pycache__"
    - ".pytest_cache"

content:
  max_token_count: 8000      # For llms.txt
  include_file_paths: true
  include_summaries: true
  preserve_code_blocks: true
  
output_template:
  header: |
    # {{ project_name }} - AI Documentation
    Generated on {{ date }} by Lift v{{ version }}
    
  section_separator: "\n---\n"
  include_metadata: true
```

### Environment Variables

```bash
# Configuration
export LIFT_CONFIG_FILE=".lift.yaml"
export LIFT_INPUT_DIR="./docs"
export LIFT_OUTPUT_DIR="./ai-ready"

# Processing limits
export LIFT_MAX_FILE_SIZE="50KB"
export LIFT_MAX_FILES=500
export LIFT_MAX_TOKENS=10000

# Output options
export LIFT_FORMAT="markdown"
export LIFT_SILENT=false
export LIFT_VERBOSE=true
```

## Integration with Other fwdslsh Tools

### Lift + Inform Workflow

Perfect for creating AI-ready documentation from web sources:

```bash
# 1. Extract content with Inform
inform https://docs.example.com \
  --output-dir extracted-docs \
  --max-pages 50

# 2. Prepare for AI with Lift
cd extracted-docs
lift --output ../ai-ready-docs

# Result: Clean, AI-optimized documentation from web sources
```

### Lift + Unify Workflow

Enhance your static site with AI-ready content:

```bash
# 1. Build documentation site with Unify
npm create unify-site docs-site
cd docs-site
bun run build

# 2. Generate AI documentation from source
lift --input src --output dist/ai

# 3. Include AI docs in your site
echo '<a href="/ai/llms.txt">AI Documentation</a>' >> dist/index.html
```

### Lift + Giv Workflow

Create comprehensive project documentation:

```bash
# 1. Generate project summary with Giv
giv summary --output PROJECT_SUMMARY.md

# 2. Create AI documentation with Lift
lift --include-code --format markdown

# 3. Generate release documentation
giv release-notes > RELEASE_NOTES.md

# 4. Combine everything for AI
lift --input . --include-patterns "*.md,PROJECT_SUMMARY.md,RELEASE_NOTES.md"
```

## Understanding the Output

### llms.txt Structure

A typical `llms.txt` file includes:

```markdown
# Project Name - AI Documentation Summary

## Project Overview
Brief description and purpose from README.md

## Key Features
- Main functionality
- Important capabilities
- Target audience

## Documentation Structure
### Getting Started
Essential setup and basic usage

### Core Concepts
Key ideas and terminology

### API/Usage Reference
Important functions, commands, or interfaces

## Project Structure
High-level organization and key files

## Additional Context
Configuration, dependencies, and environment info
```

### llms-full.txt Structure

The comprehensive version includes:

```markdown
# Project Name - Complete AI Documentation

## Metadata
- File count: 42 files processed
- Total size: 156KB
- Generated: 2024-01-15T10:30:00Z
- Lift version: 1.0.0

## Complete Documentation

### README.md
[Full content]

### docs/installation.md
[Full content]

### docs/usage.md
[Full content]

[...all other documentation files...]

## Source Code Analysis
[If --include-code is used]

### src/main.py
[Code with comments and docstrings]

## Configuration Files
[package.json, setup.py, etc.]
```

## Advanced Use Cases

### 1. Large Project Documentation

For projects with extensive documentation:

```bash
# Handle large projects efficiently
lift \
  --max-files 1000 \
  --max-file-size 500KB \
  --exclude-patterns "*.log,*.cache,node_modules/**" \
  --docs-only \
  --verbose
```

### 2. Multi-Language Projects

For projects with documentation in multiple languages:

```bash
# Process specific language documentation
lift \
  --include-patterns "docs/en/**/*.md" \
  --output ai-docs-en

lift \
  --include-patterns "docs/es/**/*.md" \
  --output ai-docs-es
```

### 3. API Documentation

For API-focused projects:

```bash
# Focus on API documentation
lift \
  --include-patterns "api/**/*.md,openapi.yaml,swagger.json" \
  --include-code \
  --preserve-structure
```

### 4. Code Analysis

For including source code in AI documentation:

```bash
# Include well-documented source code
lift \
  --include-code \
  --include-patterns "src/**/*.py,src/**/*.js" \
  --exclude-patterns "**/__pycache__/**,**/node_modules/**" \
  --max-file-size 50KB
```

## Best Practices

### 1. Optimize for AI Consumption

- **Keep documentation current**: Outdated docs confuse AI models
- **Use clear structure**: Headers, lists, and sections help AI understand context
- **Include examples**: Code examples and use cases improve AI responses
- **Remove noise**: Clean up temporary files and build artifacts before running Lift

### 2. Regular Updates

```bash
# Automate with a script
#!/bin/bash
# update-ai-docs.sh

echo "Updating AI documentation..."

# Clean up temporary files
find . -name "*.tmp" -delete
find . -name "*.log" -delete

# Generate fresh AI docs
lift --silent --output ai-docs

# Commit changes if using version control
if [ -d ".git" ]; then
    git add ai-docs/
    git commit -m "docs: Update AI documentation with Lift"
fi

echo "AI documentation updated!"
```

### 3. Team Collaboration

Create team standards for AI documentation:

**.lift.yaml (team configuration):**
```yaml
# Team-wide Lift configuration
input:
  directory: "."
  max_file_size: "100KB"

output:
  directory: "./ai-docs"
  format: "markdown"

filters:
  include_patterns:
    - "docs/**/*.md"
    - "README*"
    - "CHANGELOG*"
    - "API.md"
    - "CONTRIBUTING.md"
  
  exclude_patterns:
    - "node_modules/**"
    - "build/**"
    - "*.log"
    - "*.tmp"
    - ".env*"

content:
  max_token_count: 8000
  include_metadata: true
  preserve_code_blocks: true
```

### 4. CI/CD Integration

Add Lift to your build process:

**.github/workflows/docs.yml:**
```yaml
name: Update AI Documentation

on:
  push:
    branches: [main]
    paths: ['docs/**', 'README.md', '*.md']

jobs:
  update-ai-docs:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Generate AI Documentation
        run: |
          docker run --rm \
            -v ${{ github.workspace }}:/workspace \
            fwdslsh/toolkit:latest \
            bash -c "cd /workspace && lift --silent"
      
      - name: Commit AI Documentation
        run: |
          git config user.name "AI Docs Bot"
          git config user.email "bot@example.com"
          git add ai-docs/
          if ! git diff --cached --quiet; then
            git commit -m "docs: Update AI documentation"
            git push
          fi
```

## Troubleshooting

### Common Issues

#### Large File Warnings
```bash
# Error: File too large (>100KB)
# Solution: Increase limit or exclude large files
lift --max-file-size 500KB

# Or exclude specific large files
lift --exclude-patterns "*.pdf,*.zip,*.tar.gz"
```

#### Missing Content
```bash
# Some files not included
# Solution: Check patterns and verify file permissions
lift --verbose --dry-run

# Include specific patterns
lift --include-patterns "*.md,*.rst,*.txt,README*"
```

#### Memory Issues
```bash
# Out of memory on large projects
# Solution: Limit file count and size
lift \
  --max-files 500 \
  --max-file-size 50KB \
  --exclude-patterns "node_modules/**,build/**"
```

#### Permission Errors
```bash
# Cannot read some files
# Solution: Check file permissions
chmod -R +r docs/
lift --input docs/
```

### Performance Optimization

#### For Large Projects
```bash
# Optimize processing for speed
lift \
  --docs-only \
  --max-files 200 \
  --exclude-patterns "*.log,*.cache,build/**,node_modules/**" \
  --silent
```

#### For Better Quality
```bash
# Optimize for output quality
lift \
  --include-code \
  --preserve-structure \
  --format markdown \
  --max-token-count 12000
```

## Next Steps

### Learn More
- [Lift CLI Reference](../reference/lift-cli.md) - Complete command options
- [Configuration Guide](../guides/lift-configuration.md) - Advanced setup
- [Integration Patterns](../integrations/lift-integrations.md) - Use with other tools

### Practical Applications
- [AI Training Data Preparation](../tutorials/ai-training-prep.md) - Prepare docs for model training
- [Documentation QA Systems](../tutorials/doc-qa-systems.md) - Build question-answering systems
- [Content Analysis Workflows](../tutorials/content-analysis.md) - Analyze documentation patterns

### Community Resources
- [GitHub Repository](https://github.com/fwdslsh/lift) - Source code and issues
- [Example Configurations](../examples/lift/) - Real-world config examples
- [Community Discussions](https://github.com/orgs/fwdslsh/discussions) - Get help and share ideas

**Lift makes your documentation AI-ready!** 🤖

---

*This guide is part of the [fwdslsh examples repository](https://github.com/fwdslsh/examples). For more documentation and examples, visit the [complete guide](../getting-started.md).*