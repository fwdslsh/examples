# Contributing to fwdslsh

Thank you for your interest in contributing to the fwdslsh ecosystem! This guide will help you get started with contributing to any of our tools.

## 🌟 Ways to Contribute

### Code Contributions
- Bug fixes and improvements
- New features and enhancements
- Performance optimizations
- Test coverage improvements

### Documentation
- Fix typos and improve clarity
- Add examples and tutorials
- Translate documentation
- Create video tutorials

### Community Support
- Help answer questions in discussions
- Review pull requests
- Share examples and use cases
- Report bugs and suggest features

## 🛠️ Development Setup

### Prerequisites

- [Node.js](https://nodejs.org/) 16+ or [Bun](https://bun.sh)
- [Git](https://git-scm.com/)
- Text editor (VS Code recommended)

### Setting Up Your Environment

1. **Fork the repository** you want to contribute to
2. **Clone your fork:**
   ```bash
   git clone https://github.com/yourusername/repository-name.git
   cd repository-name
   ```

3. **Install dependencies:**
   ```bash
   # For Node.js projects
   npm install
   
   # For Bun projects
   bun install
   
   # For Python projects (like giv)
   pip install -e .
   ```

4. **Set up development environment:**
   ```bash
   # Copy environment template if it exists
   cp .env.example .env
   
   # Install pre-commit hooks if available
   npm run prepare
   ```

## 📁 Repository Structure

### Unify
```
unify/
├── src/           # Core source code
├── cli/           # CLI implementation
├── docs/          # Documentation
├── example/       # Example sites
├── tests/         # Test files
└── package.json
```

### Inform
```
inform/
├── src/           # Core crawler logic
├── cli.js         # CLI interface
├── tests/         # Test files
├── docs/          # Documentation
└── package.json
```

### Giv
```
giv/
├── src/           # Python source code
├── tests/         # Test files
├── docs/          # Documentation
├── pyproject.toml # Python project config
└── Dockerfile     # Container build
```

## 🔄 Development Workflow

### 1. Create a Feature Branch

```bash
# Create and checkout a new branch
git checkout -b feature/your-feature-name

# Or for bug fixes
git checkout -b fix/issue-description
```

### 2. Make Your Changes

- Write clear, focused commits
- Follow the existing code style
- Add tests for new functionality
- Update documentation as needed

### 3. Test Your Changes

```bash
# Run tests
npm test
# or
bun test
# or (for Python)
pytest

# Run linting
npm run lint
# or
bun run lint

# Build and test
npm run build
npm run test:integration
```

### 4. Commit Your Changes

We use [Conventional Commits](https://www.conventionalcommits.org/):

```bash
# Feature
git commit -m "feat: add new SSI directive support"

# Bug fix
git commit -m "fix: resolve path resolution on Windows"

# Documentation
git commit -m "docs: add integration guide for Netlify"

# Tests
git commit -m "test: add unit tests for file processing"
```

### 5. Push and Create Pull Request

```bash
git push origin feature/your-feature-name
```

Then create a pull request through GitHub's interface.

## 📝 Coding Standards

### JavaScript/TypeScript (Unify, Inform)

- Use ESM modules
- Follow Prettier formatting
- Use meaningful variable names
- Add JSDoc comments for public APIs
- Prefer modern JavaScript features

```javascript
/**
 * Process a template file and resolve includes
 * @param {string} filePath - Path to the template file
 * @param {Object} context - Template context variables
 * @returns {Promise<string>} Processed template content
 */
async function processTemplate(filePath, context = {}) {
  // Implementation
}
```

### Python (Giv)

- Follow PEP 8 style guide
- Use type hints
- Add docstrings for all functions
- Use meaningful variable names
- Prefer modern Python features (3.8+)

```python
def generate_commit_message(
    changes: List[GitChange], 
    template: str = "default"
) -> str:
    """Generate a commit message from git changes.
    
    Args:
        changes: List of git changes to analyze
        template: Template style to use
        
    Returns:
        Generated commit message
    """
    # Implementation
```

### Documentation

- Use clear, concise language
- Include code examples
- Add cross-references
- Keep examples up-to-date
- Use consistent formatting

## 🧪 Testing Guidelines

### Unit Tests

Write tests for all new functionality:

```javascript
// Example test structure
describe('Template Processing', () => {
  test('should resolve basic includes', async () => {
    const template = '<!--#include virtual="header.html" -->';
    const result = await processTemplate(template);
    expect(result).toContain('<header>');
  });
});
```

### Integration Tests

Test complete workflows:

```javascript
describe('Build Process', () => {
  test('should build complete site', async () => {
    const site = await buildSite('./fixtures/basic-site');
    expect(site.pages).toHaveLength(3);
    expect(site.assets).toInclude('styles.css');
  });
});
```

### Manual Testing

Always test manually with:
- Different operating systems
- Various file structures
- Edge cases and error conditions

## 📚 Documentation Guidelines

### Writing Style

- **Clear and concise**: Use simple, direct language
- **Beginner-friendly**: Assume minimal prior knowledge
- **Example-driven**: Include practical examples
- **Comprehensive**: Cover common use cases

### Structure

1. **Overview**: What the feature does
2. **Quick start**: Minimal working example
3. **Detailed usage**: All options and configurations
4. **Examples**: Real-world use cases
5. **Troubleshooting**: Common issues

### Code Examples

```markdown
# Good example format
## Basic Usage

```bash
# Install the tool
npm install -g @fwdslsh/unify

# Create a new site
unify init my-site
cd my-site

# Start development server
unify serve
```

This creates a new Unify site with...
```

## 🔍 Code Review Process

### What We Look For

- **Functionality**: Does the code work as intended?
- **Code quality**: Is the code clean and maintainable?
- **Tests**: Are there adequate tests?
- **Documentation**: Is documentation updated?
- **Performance**: Does it perform well?

### Review Checklist

- [ ] Code follows project conventions
- [ ] Tests pass and provide good coverage
- [ ] Documentation is updated
- [ ] No breaking changes (or properly documented)
- [ ] Performance impact is acceptable
- [ ] Security considerations addressed

## 🚀 Release Process

### Version Numbers

We use [Semantic Versioning](https://semver.org/):

- **MAJOR**: Breaking changes
- **MINOR**: New features (backward compatible)
- **PATCH**: Bug fixes

### Release Steps

1. **Update version**: Bump version in package.json/pyproject.toml
2. **Update changelog**: Document all changes
3. **Create release**: Tag and create GitHub release
4. **Publish**: Automated CI/CD handles publishing

## 🛡️ Security

### Reporting Security Issues

**Do not** create public issues for security vulnerabilities.

Instead:
1. Email security@fwdslsh.org
2. Provide detailed information
3. Allow time for fixes before disclosure

### Security Guidelines

- Validate all user inputs
- Sanitize file paths
- Use secure defaults
- Keep dependencies updated
- Follow security best practices

## 🎯 Project-Specific Guidelines

### Unify Contributions

Focus areas:
- Template processing performance
- New SSI directives
- Asset optimization
- Cross-platform compatibility

### Inform Contributions

Focus areas:
- Content extraction accuracy
- Website compatibility
- Performance optimization
- Export format support

### Giv Contributions

Focus areas:
- AI prompt engineering
- New output formats
- Integration with CI/CD
- Template customization

## 📋 Issue Reporting

### Bug Reports

Include:
- **Environment**: OS, Node/Bun/Python version, tool version
- **Steps to reproduce**: Exact commands and files
- **Expected behavior**: What should happen
- **Actual behavior**: What actually happens
- **Additional context**: Logs, screenshots, etc.

### Feature Requests

Include:
- **Problem description**: What problem does this solve?
- **Proposed solution**: How should it work?
- **Alternatives considered**: Other ways to solve this
- **Use cases**: Real-world examples

## 🤝 Community Guidelines

### Code of Conduct

- Be respectful and inclusive
- Focus on constructive feedback
- Help newcomers learn
- Assume good intentions

### Communication

- **GitHub Discussions**: General questions and ideas
- **Issues**: Bug reports and feature requests
- **Pull Requests**: Code contributions
- **Discord**: Real-time community chat

## 🎓 Learning Resources

### Getting Started

- [Git Handbook](https://guides.github.com/introduction/git-handbook/)
- [GitHub Flow](https://guides.github.com/introduction/flow/)
- [Conventional Commits](https://www.conventionalcommits.org/)

### Project-Specific

- [Unify Architecture](https://github.com/fwdslsh/unify/blob/main/docs/ARCHITECTURE.md)
- [Inform Design](https://github.com/fwdslsh/inform/blob/main/docs/design.md)
- [Giv Development](https://github.com/fwdslsh/giv/blob/main/docs/development.md)

## 🏆 Recognition

Contributors are recognized in:
- Project README files
- Release notes
- Annual contributor highlights
- Special badges for significant contributions

## ❓ Questions?

- 💬 [Join our discussions](https://github.com/orgs/fwdslsh/discussions)
- 📧 Email maintainers directly
- 💭 Ask in pull request comments
- 📱 Join our Discord community

Thank you for contributing to fwdslsh! Every contribution, no matter how small, helps make these tools better for everyone. 🙏