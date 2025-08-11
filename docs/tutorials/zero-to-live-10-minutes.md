# Quick Start Guide: From Zero to Live Site in 10 Minutes

Ready to build and deploy a complete website in just 10 minutes? This guide will take you from nothing to a live, professional site using the fwdslsh toolkit.

## What You'll Accomplish

By the end of this 10-minute guide, you'll have:
- ✅ A complete website running locally
- ✅ Content extracted from an existing site  
- ✅ Professional commit messages generated with AI
- ✅ A production-ready build deployed to GitHub Pages
- ✅ Understanding of the complete fwdslsh workflow

## Prerequisites (1 minute)

You'll need:
- Git installed and configured
- A GitHub account
- 10 minutes of focused time

That's it! We'll handle all the tool installation as part of the process.

---

## Step 1: Get the Toolkit (2 minutes)

We'll use the Docker toolkit for the fastest setup:

```bash
# Pull the complete fwdslsh toolkit
docker pull fwdslsh/toolkit:latest

# Create your project directory
mkdir my-10-minute-site
cd my-10-minute-site

# Start the toolkit with your project mounted
docker run -it --rm \
  -v $(pwd):/workspace \
  -p 3000:3000 \
  --name fwdslsh-quickstart \
  fwdslsh/toolkit
```

You're now inside the fwdslsh environment with all tools available!

---

## Step 2: Create Your Site (2 minutes)

```bash
# Inside the Docker container, create a new Unify site
npm create unify-site . -- --template starter

# Start the development server
bun run dev &

# Your site is now live at http://localhost:3000
echo "🎉 Your site is running! Open http://localhost:3000 in your browser"
```

**What just happened?**
- Created a complete Unify site with the starter template
- Started a development server with hot reload
- Your site includes example components, layouts, and styling

---

## Step 3: Add Content with AI (2 minutes)

Let's extract some real content to make your site more interesting:

```bash
# Extract content from a documentation site (example: Unify docs)
inform https://github.com/fwdslsh/unify/blob/main/README.md \
  --output-dir extracted-content \
  --max-pages 5

# Move the extracted content to your site
cp -r extracted-content/* src/pages/

# The development server automatically updates!
# Check http://localhost:3000 to see your new content
```

**Pro tip**: You can extract from any website! Try:
- Documentation sites: `inform https://docs.example.com`
- Blogs: `inform https://blog.company.com --max-pages 10`
- Knowledge bases: `inform https://help.service.com`

---

## Step 4: Professional Git Workflow (1 minute)

Initialize version control and use AI to generate professional commit messages:

```bash
# Set up your Git repository
git init
git add .

# Let AI generate a professional commit message
export OPENAI_API_KEY="your-api-key"  # Optional: for AI features
giv message

# Example AI-generated message:
# "feat: Initialize Unify site with starter template
# 
# - Set up basic project structure with src/ directory
# - Add example components and layouts
# - Configure development server with hot reload
# - Include extracted content from external documentation"

# Commit your changes
git commit -F .giv-message
```

**Don't have an API key?** No problem! Giv will still work but will prompt you to write the commit message manually.

---

## Step 5: Build and Deploy (2 minutes)

Let's build your site for production and deploy it to GitHub Pages:

```bash
# Build optimized production site
bun run build

# Your production site is now in the dist/ folder
ls dist/  # See your built files

# Create a GitHub repository and deploy
gh auth login  # Authenticate with GitHub (if not already done)

# Create repository
gh repo create my-10-minute-site --public --push --source=.

# Enable GitHub Pages
gh api repos/:owner/my-10-minute-site/pages \
  --method POST \
  --field source.branch=main \
  --field source.path=/

# Generate release notes
giv release-notes > RELEASE_NOTES.md

# Final commit with release info
git add .
giv message  # "docs: Add release notes for initial deployment"
git commit -F .giv-message
git push
```

**Your site is now live!** 🚀

Visit: `https://yourusername.github.io/my-10-minute-site`

---

## 🎉 Congratulations!

You've just completed a full modern web development workflow in 10 minutes:

### ✅ What You Built
- **Modern Static Site**: Fast, responsive, and production-ready
- **Component Architecture**: Reusable components with slots and includes
- **Content Management**: AI-extracted content properly formatted
- **Professional Git History**: AI-generated commit messages and documentation
- **Live Deployment**: Automatically deployed to GitHub Pages with custom domain support

### ✅ Tools You Used
- **Unify**: Static site generation with zero configuration
- **Inform**: Intelligent content extraction and conversion
- **Giv**: AI-powered Git workflow automation
- **Toolkit**: All tools in one convenient container

### ✅ Skills You Learned
- Component-based site architecture
- Content extraction and migration workflows
- AI-assisted development practices
- Modern deployment workflows

---

## What's Next? (Choose Your Adventure)

### 🎨 Customize Your Site (15 minutes)
- [Customize Components and Layouts](../unify/advanced-templating.md)
- [Add Custom Styling and Branding](../unify/styling-guide.md)
- [Optimize for SEO and Performance](../unify/seo-optimization.md)

### 📝 Add More Content (20 minutes)
- [Extract from Multiple Sources](../inform/batch-extraction.md)
- [Set Up a Blog with Dynamic Content](../tutorials/unify-blog.md)
- [Create a Documentation Site](../tutorials/unify-docs-site.md)

### 🚀 Advanced Workflows (30+ minutes)
- [Set Up CI/CD for Automatic Deployments](../integrations/github-actions.md)
- [Create Multi-Language Sites](../unify/internationalization.md)
- [Build a Complete Documentation Portal](../tutorials/enterprise-docs.md)

### 🤖 Master AI-Assisted Development (45 minutes)
- [Advanced Giv Configuration](../giv/advanced-configuration.md)
- [Custom AI Prompts for Your Team](../giv/custom-prompts.md)
- [Automate Release Management](../giv/release-automation.md)

---

## Troubleshooting

### Docker Issues
```bash
# If Docker container won't start
docker ps -a  # Check container status
docker logs fwdslsh-quickstart  # Check logs

# If port is already in use
docker run -p 3001:3000 fwdslsh/toolkit  # Use different port
```

### Build Issues
```bash
# If Unify build fails
bun run clean  # Clear cache
bun run build --verbose  # Detailed output

# If npm create fails
npx create-unify-site@latest .  # Use npx directly
```

### Git/GitHub Issues
```bash
# If GitHub CLI authentication fails
gh auth login --web  # Use web authentication

# If Pages deployment fails
# Check repository settings > Pages in GitHub UI
# Ensure source is set to "Deploy from a branch" > main
```

### No AI Features?
```bash
# Giv will work without API keys but with reduced functionality
# For full AI features, get an API key from:
# - OpenAI: https://platform.openai.com/api-keys
# - Anthropic: https://console.anthropic.com/

export OPENAI_API_KEY="your-key-here"
# or
export ANTHROPIC_API_KEY="your-key-here"
```

---

## Alternative Paths

### Native Installation (If you prefer not to use Docker)

```bash
# Install Bun
curl -fsSL https://bun.sh/install | bash

# Install tools
bun add -g @fwdslsh/unify @fwdslsh/inform
pip install giv

# Create site
npm create unify-site my-site
cd my-site
bun run dev
```

### GitHub Codespaces (Cloud Development)

1. Create a new repository on GitHub
2. Click "Code" > "Codespaces" > "Create codespace"
3. Run the Docker commands in the cloud environment
4. Develop entirely in your browser!

### Replit/GitPod (Alternative Cloud Options)

The fwdslsh toolkit works in any environment that supports Docker.

---

## Real-World Examples

### Documentation Site (Like This One!)
```bash
# Extract existing docs
inform https://docs.yourcompany.com --output-dir content

# Create docs site
npm create unify-site company-docs -- --template docs
# ... customize and deploy
```

### Product Landing Page
```bash
# Extract competitor research
inform https://competitor.com/product --output-dir research

# Create marketing site
npm create unify-site product-site -- --template landing
# ... build your unique value proposition
```

### Personal Portfolio
```bash
# Create portfolio site
npm create unify-site portfolio -- --template portfolio

# Add your projects and experience
# Deploy to showcase your work
```

---

## Join the Community

### Get Help
- 💬 [GitHub Discussions](https://github.com/orgs/fwdslsh/discussions)
- 📖 [Complete Documentation](../getting-started.md)
- 🐛 [Report Issues](https://github.com/fwdslsh/examples/issues)

### Stay Updated
- ⭐ Star the [fwdslsh repositories](https://github.com/fwdslsh)
- 📧 Follow [@fwdslsh](https://github.com/fwdslsh) for updates
- 🔔 Watch repositories for new features and releases

### Contribute
- 📝 Improve documentation
- 🧪 Test new features
- 💡 Share your use cases
- 🛠️ Contribute code improvements

---

## Summary: Your 10-Minute Achievement

In just 10 minutes, you:

1. **⚡ Set up a complete development environment** with zero configuration
2. **🏗️ Built a modern website** with component architecture
3. **📝 Extracted and integrated real content** using AI-powered tools
4. **🤖 Automated your Git workflow** with intelligent commit messages
5. **🚀 Deployed to production** with global CDN distribution

**You now have the foundation to build any kind of website or documentation project with the fwdslsh ecosystem.**

The tools scale from simple personal sites to enterprise documentation portals, and you can adopt them gradually or all at once.

**Welcome to the future of web development!** 🌟

---

*This guide is part of the [fwdslsh examples repository](https://github.com/fwdslsh/examples). For more tutorials and examples, explore the [complete documentation](../getting-started.md).*