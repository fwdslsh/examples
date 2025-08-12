# Custom Prompt Engineering for Development Teams

Master the art of creating team-specific templates and workflows with Giv's powerful prompt engineering system. This comprehensive guide shows you how to customize AI interactions for your development team's unique needs.

## Understanding Prompt Engineering

### Principles of Effective Prompt Design

**1. Context and Specificity**
- Provide clear context about your codebase and team practices
- Use specific terminology and conventions
- Include examples of desired output format

**2. Structure and Consistency**
- Maintain consistent template formats
- Use clear variable placeholders
- Follow logical information hierarchy

**3. Error Prevention**
- Handle edge cases gracefully
- Provide fallback content
- Validate template syntax

### Common Pitfalls and Solutions

**❌ Vague prompts lead to generic output**
```markdown
Generate a commit message for these changes.
```

**✅ Specific prompts produce targeted results**
```markdown
Generate a conventional commit message for this API change. Focus on:
- Breaking changes (if any)
- New endpoints or modified behavior  
- Impact on existing clients
Format: type(scope): description
```

## Giv Prompt System Architecture

### Template Hierarchy and Inheritance

Giv supports a hierarchical configuration system:

```
Global Config (~/.giv.yaml)
    ↓
Project Config (.giv.yaml)
    ↓
Team Templates (.giv-templates/)
    ↓
Command-specific overrides (--template flag)
```

### Template File Structure

**Basic template structure:**
```markdown
# template.md
---
name: "Custom Template Name"
description: "Template description"
variables:
  - project_type
  - team_standards
---

{{ system_prompt }}

Generate a {{ output_type }} for the following changes:

{{ changes }}

Requirements:
- Follow {{ team_standards }}
- Include {{ project_type }} specific details
- Use our team's tone: {{ tone }}

{{ format_requirements }}
```

### Environment-Specific Configurations

**Development Environment:**
```yaml
# .giv.yaml (development)
api:
  model: "gpt-3.5-turbo"  # Faster, cheaper for dev
  temperature: 0.8        # More creative

commit:
  template_file: ".giv-templates/dev-commit.md"
  include_debug: true
```

**Production Environment:**
```yaml
# .giv.yaml (production)
api:
  model: "gpt-4"         # More accurate for releases
  temperature: 0.3       # More conservative

commit:
  template_file: ".giv-templates/release-commit.md"
  include_debug: false
```

## Development Team Use Cases

### Commit Message Standardization

**Team-specific commit template:**

```markdown
# .giv-templates/team-commit.md
---
name: "Team Commit Standard"
description: "Our team's commit message format"
---

You are a senior developer on our {{ team_name }} team. Generate a conventional commit message following our standards:

CONTEXT:
- Project: {{ project_name }} ({{ project_type }})
- Team: {{ team_name }}
- Sprint: {{ current_sprint }}

CHANGES:
{{ diff_summary }}

REQUIREMENTS:
1. Use conventional commits format: type(scope): description
2. Types we use: feat, fix, docs, style, refactor, test, chore, security
3. Always include scope when applicable
4. Description should be imperative mood, lowercase, no period
5. Include BREAKING CHANGE footer if applicable
6. Reference ticket: Closes #{{ ticket_number }}

EXAMPLES:
- feat(auth): add OAuth2 integration with Google
- fix(api): resolve race condition in user creation
- docs(readme): update installation instructions

Generate the commit message:
```

**Usage:**
```bash
# Use team template
giv message --template .giv-templates/team-commit.md

# Set as default in .giv.yaml
commit:
  template_file: ".giv-templates/team-commit.md"
```

### Documentation Generation Patterns

**API documentation template:**
```markdown
# .giv-templates/api-docs.md
---
name: "API Documentation"
description: "Generate API documentation from code changes"
---

You are a technical writer creating API documentation. Analyze these changes and generate documentation:

{{ code_changes }}

Create documentation that includes:

## API Changes Summary
[High-level overview of what changed]

## New Endpoints
[List new endpoints with HTTP methods]

## Modified Endpoints  
[List changed endpoints with what changed]

## Breaking Changes
[Any breaking changes with migration notes]

## Examples
[Code examples showing usage]

## Migration Guide
[Step-by-step migration if needed]

Format as clean markdown suitable for our documentation site.
```

### Code Review Assistance

**Pull request description template:**
```markdown
# .giv-templates/pr-description.md
---
name: "PR Description"
description: "Generate comprehensive PR descriptions"
---

Analyze this pull request and create a comprehensive description:

## Changes
{{ diff_summary }}

## Branch Info
- From: {{ source_branch }}
- To: {{ target_branch }}
- Commits: {{ commit_count }}

Generate a pull request description with:

## Summary
[2-3 sentences explaining the change]

## What Changed
- [Bulleted list of key changes]
- [Focus on user/developer impact]

## Testing
- [How this was tested]
- [What scenarios were covered]

## Deployment Notes
- [Any deployment considerations]
- [Environment variables, migrations, etc.]

## Checklist
- [ ] Tests added/updated
- [ ] Documentation updated
- [ ] Breaking changes documented
- [ ] Security review completed (if applicable)

Keep it concise but comprehensive. Focus on WHY these changes were made.
```

## Advanced Prompt Techniques

### Few-Shot Learning Examples

**Enhanced commit message template with examples:**

```markdown
# .giv-templates/few-shot-commit.md
---
name: "Few-Shot Learning Commit"
description: "Uses examples to improve output quality"
---

Generate a conventional commit message. Here are examples of good commits from our team:

EXAMPLE 1:
Changes: Added user authentication endpoints
Message: feat(auth): implement JWT-based user authentication

- Add login/logout endpoints
- Implement JWT token generation and validation  
- Add middleware for protected routes

EXAMPLE 2:
Changes: Fixed memory leak in background job processor
Message: fix(jobs): resolve memory leak in background processor

- Fix unclosed database connections in job handler
- Add proper cleanup in error conditions
- Update job processing tests

EXAMPLE 3:
Changes: Updated README with new installation steps
Message: docs(readme): update installation instructions

- Add prerequisites section
- Update npm install command
- Add troubleshooting guide

NOW GENERATE:
Changes: {{ changes }}
Message: [Generate following the pattern above]
```

### Chain-of-Thought Prompting

**Complex analysis template:**
```markdown
# .giv-templates/chain-of-thought.md
---
name: "Chain of Thought Analysis"
description: "Break down complex changes step by step"
---

Analyze these changes using chain-of-thought reasoning:

{{ changes }}

Let me think through this step by step:

1. **What files were changed?**
   [List the files and their purposes]

2. **What type of changes were made?**
   [Categorize: new features, bug fixes, refactoring, etc.]

3. **What is the business impact?**
   [How does this affect users or the product?]

4. **Are there any risks or considerations?**
   [Breaking changes, performance impact, etc.]

5. **What testing is needed?**
   [What should be tested to verify this works?]

Based on this analysis, generate:
- A conventional commit message
- A brief explanation of the change
- Any risks or considerations for reviewers
```

### Error Handling and Fallbacks

**Robust template with fallbacks:**
```markdown
# .giv-templates/robust-commit.md
---
name: "Robust Commit Template"
description: "Handles various scenarios gracefully"
---

Generate a commit message for these changes:

{{ changes }}

Instructions:
1. If no meaningful changes detected, respond: "No significant changes to commit"
2. If changes are too complex to summarize, focus on the primary change
3. If unsure about the type, use "chore" as fallback
4. Always include a scope if one is apparent from file paths

Template:
```
type(scope): description

[optional body]

[optional footer]
```

Guidelines:
- Keep description under 72 characters
- Use imperative mood (add, fix, update)  
- No capital letters or periods in description
- Include breaking change notes if applicable

Fallback types if unsure:
- chore: for maintenance tasks
- refactor: for code restructuring
- style: for formatting changes
```

## Team-Specific Customization

### Domain-Specific Terminology

**Frontend team template:**
```yaml
# .giv.yaml for frontend team
commit:
  template: |
    You are a frontend developer. Use these terms:
    - "component" not "widget"
    - "state management" not "data handling"
    - "accessibility" includes ARIA, WCAG compliance
    - "performance" includes bundle size, rendering
    
    Common scopes: ui, components, state, routing, assets, tests
    
    Generate commit: {{ type }}({{ scope }}): {{ description }}
    
    Changes: {{ changes }}
```

**Backend team template:**
```yaml
# .giv.yaml for backend team  
commit:
  template: |
    You are a backend developer. Use these terms:
    - "endpoint" not "page"
    - "service" for business logic
    - "repository" for data access
    - "middleware" for request processing
    
    Common scopes: api, auth, db, cache, queue, config
    
    Generate commit: {{ type }}({{ scope }}): {{ description }}
    
    Changes: {{ changes }}
```

### Project-Specific Patterns

**Microservice architecture:**
```markdown
# .giv-templates/microservice.md
---
name: "Microservice Commit"
description: "Commit messages for microservice projects"
---

You're working on a microservice architecture. Analyze these changes:

{{ changes }}

Service context:
- Service: {{ service_name }}
- Dependencies: {{ service_dependencies }}
- Communication: {{ communication_pattern }}

Generate a commit message that:
1. Includes the service name in scope
2. Mentions cross-service impacts
3. Notes any API contract changes
4. Includes deployment implications

Format: type(service-name): description

Examples:
- feat(user-service): add password reset endpoint
- fix(payment-service): resolve timeout in billing integration  
- chore(auth-service): update JWT library to v2.1
```

### Role-Based Prompt Variations

**For junior developers:**
```markdown
# .giv-templates/junior-friendly.md
---
name: "Junior Developer Template"
description: "Educational commit messages"
---

Generate an educational commit message that helps junior developers understand:

Changes: {{ changes }}

Include:
1. **What changed** (simple explanation)
2. **Why it changed** (business reason)
3. **How it works** (brief technical note)

Format:
```
type(scope): what changed

Why: [business reason]
How: [technical explanation]
```

Example:
```
feat(auth): add password validation

Why: Users need secure passwords to protect accounts
How: Added regex validation and strength checking
```

**For senior developers:**
```markdown
# .giv-templates/senior-focused.md
---
name: "Senior Developer Template"
description: "Technical depth for experienced developers"
---

Generate a technical commit message focusing on:

Changes: {{ changes }}

Include:
- Architecture implications
- Performance considerations  
- Security implications
- Testing strategy
- Deployment impact

Keep it concise but technically precise. Assume deep technical knowledge.
```

## Testing and Optimization

### Prompt Effectiveness Measurement

**Create test scenarios:**
```bash
# Test your templates with various scenarios
mkdir -p .giv-test-scenarios

# Create test cases
cat > .giv-test-scenarios/scenario1.patch << 'EOF'
diff --git a/src/auth.js b/src/auth.js
+++ b/src/auth.js
@@ -10,6 +10,12 @@ function login(username, password) {
   // existing code
+  
+  // Add password validation
+  if (!isValidPassword(password)) {
+    throw new Error('Invalid password format');
+  }
 }
EOF

# Test template
giv message --template .giv-templates/team-commit.md --diff .giv-test-scenarios/scenario1.patch
```

**A/B Testing Methodology:**

```bash
#!/bin/bash
# test-templates.sh

templates=("template-a.md" "template-b.md")
scenarios=(.giv-test-scenarios/*.patch)

for template in "${templates[@]}"; do
  echo "Testing $template:"
  for scenario in "${scenarios[@]}"; do
    echo "  Scenario: $(basename $scenario)"
    result=$(giv message --template ".giv-templates/$template" --diff "$scenario")
    echo "  Result: $result"
    echo "  Rating: [1-5] ___"
    echo
  done
done
```

### Continuous Improvement Processes

**Template versioning:**
```bash
# Version your templates
mkdir -p .giv-templates/versions/v1.0
cp .giv-templates/*.md .giv-templates/versions/v1.0/

# Create changelog for template changes
cat > .giv-templates/CHANGELOG.md << 'EOF'
# Template Changelog

## v1.1 - 2024-02-15
- Added error handling examples
- Improved commit message consistency
- Added support for breaking changes

## v1.0 - 2024-01-15
- Initial team templates
- Basic commit message format
- Simple variable substitution
EOF
```

**Performance tracking:**
```yaml
# .giv.yaml
metrics:
  track_template_performance: true
  log_generation_time: true
  collect_feedback: true
  
feedback:
  prompt_after_commit: true
  rating_scale: 1-5
  feedback_file: ".giv-metrics/feedback.json"
```

## Team Collaboration Setup

### Shared Template Repository

**Create team template repository:**
```bash
# Initialize team templates repo
git init giv-team-templates
cd giv-team-templates

mkdir -p templates/{commit,changelog,release,pr}

# Create shared templates
cat > templates/commit/standard.md << 'EOF'
# Team Standard Commit Template
[Template content here]
EOF

# Version and distribute
git add .
git commit -m "feat: initial team templates"
git tag v1.0.0
```

**Team installation:**
```bash
# Each team member installs templates
git clone git@company.com:team/giv-team-templates.git ~/.giv-templates
ln -sf ~/.giv-templates/templates/commit/standard.md .giv-templates/commit.md
```

### Configuration Management

**Team configuration distribution:**
```yaml
# .giv.yaml (committed to each project)
api:
  model: "${GIV_MODEL:-gpt-4}"
  temperature: 0.5

commit:
  template_file: "${GIV_TEMPLATE_PATH:-~/.giv-templates/commit.md}"
  require_scope: true
  allowed_types: ["feat", "fix", "docs", "style", "refactor", "test", "chore"]

changelog:
  format: "keepachangelog"
  group_by_type: true
  include_authors: false

team:
  name: "${TEAM_NAME:-development}"
  standards_url: "https://wiki.company.com/dev-standards"
```

### Training and Onboarding

**Template training session:**
```markdown
# Template Training Guide

## Session 1: Basics (30 minutes)
1. What are Giv templates?
2. How templates improve consistency
3. Installing team templates
4. Basic template syntax

## Session 2: Customization (45 minutes)
1. Variable substitution
2. Conditional logic
3. Team-specific requirements
4. Testing templates

## Session 3: Advanced (60 minutes)
1. Complex prompt engineering
2. Chain-of-thought techniques
3. Error handling
4. Performance optimization

## Hands-on Exercise
- Create a template for your team's needs
- Test with real scenarios
- Share and get feedback
- Iterate and improve
```

## Best Practices

### Template Design Guidelines

**1. Keep Templates Focused**
```markdown
✅ Good: One template per use case
❌ Bad: Monolithic template trying to handle everything
```

**2. Use Clear Variable Names**
```markdown
✅ Good: {{ project_name }}, {{ team_standards }}
❌ Bad: {{ x }}, {{ thing }}, {{ stuff }}
```

**3. Provide Context**
```markdown
✅ Good: "You are a senior developer on the {{ team_name }} team working on {{ project_type }}"
❌ Bad: "Generate a commit message"
```

**4. Include Examples**
```markdown
✅ Good: Show 2-3 examples of desired output
❌ Bad: Expect the AI to guess your preferences
```

### Quality Assurance

**Template review checklist:**
- [ ] Clear, specific instructions
- [ ] Proper variable usage
- [ ] Error handling included
- [ ] Examples provided
- [ ] Team standards reflected
- [ ] Tested with various scenarios
- [ ] Performance acceptable
- [ ] Documentation updated

### Maintenance Strategy

**Regular template audits:**
```bash
#!/bin/bash
# template-audit.sh

echo "Giv Template Audit Report"
echo "========================="
echo "Date: $(date)"
echo

# Check template usage
echo "Most used templates:"
grep -r "template_file" ~/.giv ~/.giv.yaml 2>/dev/null | sort | uniq -c | sort -nr

# Check for outdated templates
echo
echo "Templates older than 90 days:"
find .giv-templates -name "*.md" -mtime +90

# Check for unused templates
echo
echo "Potentially unused templates:"
for template in .giv-templates/*.md; do
  if ! grep -q "$(basename $template)" .giv.yaml ~/.giv.yaml 2>/dev/null; then
    echo "  $template"
  fi
done
```

## Advanced Integration Patterns

### CI/CD Integration

**Automated template validation:**
```yaml
# .github/workflows/template-validation.yml
name: Validate Giv Templates

on:
  pull_request:
    paths:
      - '.giv-templates/**'
      - '.giv.yaml'

jobs:
  validate-templates:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Install Giv
        run: pip install giv
        
      - name: Validate templates
        run: |
          for template in .giv-templates/*.md; do
            echo "Validating $template"
            giv message --template "$template" --diff <(echo "test change") --dry-run
          done
```

### IDE Integration

**VS Code snippet for template creation:**
```json
{
  "Giv Template": {
    "prefix": "giv-template",
    "body": [
      "# ${1:Template Name}",
      "---",
      "name: \"${1:Template Name}\"", 
      "description: \"${2:Template description}\"",
      "---",
      "",
      "You are a ${3:role} working on ${4:context}.",
      "",
      "Generate a ${5:output_type} for:",
      "{{ changes }}",
      "",
      "Requirements:",
      "- ${6:requirement1}",
      "- ${7:requirement2}",
      "",
      "${8:additional_instructions}"
    ],
    "description": "Create a new Giv template"
  }
}
```

## Troubleshooting Common Issues

### Template Not Working

**Debug template execution:**
```bash
# Enable debug mode
export GIV_DEBUG=1
giv message --template problematic-template.md

# Test template syntax
giv config validate-template .giv-templates/template.md

# Check variable substitution
giv message --template template.md --show-rendered-prompt
```

### Inconsistent Output

**Common causes and solutions:**
1. **Temperature too high** → Lower to 0.3-0.5
2. **Vague instructions** → Add more specific requirements
3. **No examples** → Include 2-3 good examples
4. **Context missing** → Add project/team context

### Performance Issues

**Optimize slow templates:**
```bash
# Profile template performance
time giv message --template slow-template.md

# Reduce template complexity
# - Remove unnecessary instructions
# - Simplify variable substitution
# - Use more efficient prompting patterns
```

## Next Steps

### Advanced Topics
- [Giv API Integration](api-integration.md) - Integrate with external systems
- [Multi-Model Templates](multi-model-templates.md) - Use different AI models for different tasks
- [Template Analytics](template-analytics.md) - Measure and improve template performance

### Team Resources
- [Template Gallery](../examples/giv/templates/) - Community-contributed templates
- [Best Practices Guide](best-practices.md) - Advanced optimization techniques
- [Troubleshooting Guide](../troubleshooting/giv.md) - Common issues and solutions

### Community
- [Share your templates](https://github.com/orgs/fwdslsh/discussions) - Contribute to the community
- [Template requests](https://github.com/fwdslsh/giv/issues) - Request new template features
- [Training materials](../training/) - Resources for team adoption

**Master prompt engineering with Giv and transform your development workflow!** 🚀

---

*This guide is part of the [fwdslsh documentation](https://fwdslsh.dev). For more advanced techniques and team setups, explore our [comprehensive Giv documentation](../giv/).*