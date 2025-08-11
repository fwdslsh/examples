# 5-Minute Unify Tutorial

Get up and running with Unify, the modern static site generator, in just 5 minutes! This tutorial will take you from installation to your first live site.

## What You'll Build

By the end of this tutorial, you'll have:
- A working Unify site running locally
- Understanding of the basic project structure
- Your first custom page with components
- Knowledge of the development workflow

## Prerequisites

- [Bun](https://bun.sh) installed on your system
- Basic knowledge of HTML/CSS
- 5 minutes of your time!

## Step 1: Create Your Site (1 minute)

```bash
# Create a new Unify site
npm create unify-site my-first-site
cd my-first-site

# Start the development server
bun run dev
```

🎉 **Your site is now live at http://localhost:3000!**

The `create-unify-site` command gives you:
- Pre-configured project structure
- Example components and layouts
- Development server with hot reload
- Build scripts for production

## Step 2: Explore the Structure (1 minute)

Your new project has this structure:

```
my-first-site/
├── src/
│   ├── .components/     # Reusable components
│   │   ├── layout.html  # Main page layout
│   │   └── header.html  # Site header
│   ├── index.html       # Homepage
│   └── about.html       # About page
├── package.json
└── unify.config.js      # Unify configuration
```

**Key Concepts:**
- `.components/` - Reusable HTML components with slots
- `src/` - All your site content goes here
- Components use `<!-- @include -->` for inclusion
- Hot reload updates instantly as you edit

## Step 3: Make Your First Edit (1 minute)

Let's customize the homepage:

1. Open `src/index.html` in your editor
2. Find the main content section
3. Replace it with your own content:

```html
<!DOCTYPE html>
<html>
<head>
    <title>My First Unify Site</title>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
</head>
<body>
    <!-- @include .components/header.html -->
    
    <main>
        <h1>Welcome to My Unify Site!</h1>
        <p>This site was built in just 5 minutes with Unify.</p>
        
        <div class="features">
            <h2>Why Unify?</h2>
            <ul>
                <li>🚀 Fast builds with Bun</li>
                <li>🔥 Hot reload development</li>
                <li>📦 Component-based architecture</li>
                <li>✨ Zero learning curve</li>
            </ul>
        </div>
    </main>
    
    <footer>
        <p>&copy; 2024 My First Unify Site</p>
    </footer>
</body>
</html>
```

Save the file and watch your browser update instantly!

## Step 4: Create a Component (1 minute)

Let's create a reusable component for the features section:

1. Create `src/.components/features.html`:

```html
<div class="features">
    <h2>{{ title || "Features" }}</h2>
    <div class="feature-list">
        <!-- @slot -->
        <p>Add your features here!</p>
        <!-- @endslot -->
    </div>
</div>

<style>
.features {
    background: #f5f5f5;
    padding: 2rem;
    border-radius: 8px;
    margin: 2rem 0;
}

.feature-list {
    margin-top: 1rem;
}
</style>
```

2. Update `src/index.html` to use the component:

```html
<main>
    <h1>Welcome to My Unify Site!</h1>
    <p>This site was built in just 5 minutes with Unify.</p>
    
    <!-- @include .components/features.html title="Why Unify?" -->
    <ul>
        <li>🚀 Fast builds with Bun</li>
        <li>🔥 Hot reload development</li>
        <li>📦 Component-based architecture</li>
        <li>✨ Zero learning curve</li>
    </ul>
    <!-- @endinclude -->
</main>
```

Your component system is now working! The features section has its own styling and can be reused anywhere.

## Step 5: Build for Production (1 minute)

Ready to deploy your site?

```bash
# Build optimized site
bun run build

# Your production site is in the dist/ folder
ls dist/

# Test the production build locally
bun run preview
```

The `dist/` folder contains your complete static site, ready to deploy to:
- GitHub Pages
- Netlify
- Vercel
- Any static hosting provider

## 🎉 Congratulations!

In just 5 minutes, you've:
- ✅ Created a Unify site from scratch
- ✅ Learned the project structure
- ✅ Made custom content changes
- ✅ Created and used a component
- ✅ Built for production

## What's Next?

Now that you know the basics, explore these next steps:

### Quick Wins (5-10 minutes each)
- [Building a Blog with Unify](../tutorials/unify-blog.md) - Add a blog to your site
- [Creating a Portfolio Site](../tutorials/unify-portfolio.md) - Showcase your work
- [Unify + GitHub Pages](../integrations/unify-github-pages.md) - Deploy for free

### Advanced Features (30+ minutes)
- [Advanced Templating Guide](advanced-templating.md) - Master slots, includes, and layouts
- [SEO Optimization](seo-optimization.md) - Optimize for search engines  
- [Performance Tuning](performance-optimization.md) - Speed up large sites

### Real Projects
- [Documentation Site Tutorial](../tutorials/unify-docs-site.md) - Build a full documentation site
- [Marketing Site Examples](../examples/unify/marketing-sites/) - See real-world implementations

## Need Help?

- 📖 [Full Documentation](user-guide.md)
- 🐛 [Troubleshooting](../troubleshooting/unify.md)
- 💬 [Community Discussions](https://github.com/orgs/fwdslsh/discussions)
- 🚨 [Report Issues](https://github.com/fwdslsh/unify/issues)

**Happy building with Unify!** 🚀