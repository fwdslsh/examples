# Creating Reusable Components with Unify

Master Unify's component system to build maintainable, reusable web components without the complexity of modern frameworks. This guide covers everything from basic includes to advanced slot management.

## Overview

Unify's component system provides:
- **Server-side includes** for component composition
- **Slot system** for flexible content injection
- **Layout inheritance** for consistent page structure  
- **Component discovery** with automatic registration
- **Development-time validation** and error checking

## Basic Component Creation

### Creating Your First Component

Create a simple header component:

**`src/.components/header.html`**:
```html
<!-- Header Component -->
<header class="site-header">
  <div class="container">
    <h1 class="logo">
      <slot name="logo">
        <a href="/">My Site</a>
      </slot>
    </h1>
    
    <nav class="navigation">
      <slot name="navigation">
        <ul>
          <li><a href="/">Home</a></li>
          <li><a href="/about">About</a></li>
          <li><a href="/contact">Contact</a></li>
        </ul>
      </slot>
    </nav>
  </div>
</header>
```

### Using Components in Pages

**`src/index.html`**:
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Home Page</title>
</head>
<body>
  <!-- Include the header component -->
  <div data-include=".components/header.html">
    <div slot="logo">
      <a href="/"><img src="/logo.png" alt="Company Logo"></a>
    </div>
    <div slot="navigation">
      <ul>
        <li><a href="/" class="active">Home</a></li>
        <li><a href="/products">Products</a></li>
        <li><a href="/services">Services</a></li>
        <li><a href="/contact">Contact</a></li>
      </ul>
    </div>
  </div>

  <main>
    <h1>Welcome to our website!</h1>
    <p>This page uses reusable components.</p>
  </main>
</body>
</html>
```

## Advanced Component Patterns

### Complex Components with Multiple Slots

Create a card component with flexible content areas:

**`src/.components/card.html`**:
```html
<!-- Flexible Card Component -->
<article class="card">
  <header class="card-header">
    <slot name="header">
      <h3>Default Title</h3>
    </slot>
  </header>
  
  <div class="card-media">
    <slot name="media">
      <!-- Optional media slot -->
    </slot>
  </div>
  
  <div class="card-content">
    <slot name="content">
      <p>Default card content goes here.</p>
    </slot>
  </div>
  
  <footer class="card-footer">
    <slot name="actions">
      <button class="btn btn-primary">Learn More</button>
    </slot>
  </footer>
</article>
```

**Usage with different content**:
```html
<!-- Product card -->
<div data-include=".components/card.html">
  <div slot="header">
    <h3>Amazing Product</h3>
    <span class="badge">New</span>
  </div>
  <div slot="media">
    <img src="/product.jpg" alt="Product image">
  </div>
  <div slot="content">
    <p>This product will revolutionize your workflow.</p>
    <ul>
      <li>Feature 1</li>
      <li>Feature 2</li>
      <li>Feature 3</li>
    </ul>
  </div>
  <div slot="actions">
    <button class="btn btn-primary">Buy Now</button>
    <button class="btn btn-secondary">Learn More</button>
  </div>
</div>

<!-- Blog post card -->
<div data-include=".components/card.html">
  <div slot="header">
    <h3>Blog Post Title</h3>
    <time datetime="2024-01-15">January 15, 2024</time>
  </div>
  <div slot="content">
    <p>An excerpt from the blog post...</p>
  </div>
  <div slot="actions">
    <a href="/blog/post-slug" class="btn btn-primary">Read More</a>
  </div>
</div>
```

### Nested Components

Components can include other components for complex layouts:

**`src/.components/layout.html`**:
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title><slot name="title">Default Title</slot></title>
  <link rel="stylesheet" href="/styles.css">
  <slot name="head">
    <!-- Additional head content -->
  </slot>
</head>
<body>
  <!-- Include header component -->
  <div data-include=".components/header.html">
    <slot name="header-content">
      <!-- Default header content -->
    </slot>
  </div>

  <!-- Main content area -->
  <main class="main-content">
    <slot name="content">
      <h1>Welcome</h1>
      <p>Default page content.</p>
    </slot>
  </main>

  <!-- Include footer component -->
  <div data-include=".components/footer.html">
    <slot name="footer-content">
      <!-- Default footer content -->
    </slot>
  </div>

  <slot name="scripts">
    <!-- Additional scripts -->
  </slot>
</body>
</html>
```

### Conditional Components

Use CSS and JavaScript for conditional rendering:

**`src/.components/alert.html`**:
```html
<!-- Alert Component with Types -->
<div class="alert" data-type="info">
  <div class="alert-icon">
    <slot name="icon">
      <svg class="icon"><!-- default icon --></svg>
    </slot>
  </div>
  
  <div class="alert-content">
    <div class="alert-title">
      <slot name="title">Notice</slot>
    </div>
    <div class="alert-message">
      <slot name="message">
        This is an informational message.
      </slot>
    </div>
  </div>
  
  <button class="alert-close" onclick="this.parentElement.remove()">
    <slot name="close-icon">×</slot>
  </button>
</div>

<style>
  .alert {
    display: flex;
    align-items: center;
    padding: 1rem;
    border-radius: 0.5rem;
    margin: 1rem 0;
  }
  
  .alert[data-type="info"] {
    background-color: #e3f2fd;
    border-left: 4px solid #2196f3;
    color: #1976d2;
  }
  
  .alert[data-type="success"] {
    background-color: #e8f5e8;
    border-left: 4px solid #4caf50;
    color: #388e3c;
  }
  
  .alert[data-type="warning"] {
    background-color: #fff3e0;
    border-left: 4px solid #ff9800;
    color: #f57c00;
  }
  
  .alert[data-type="error"] {
    background-color: #ffebee;
    border-left: 4px solid #f44336;
    color: #d32f2f;
  }
</style>
```

**Usage**:
```html
<!-- Success alert -->
<div data-include=".components/alert.html" data-type="success">
  <div slot="title">Success!</div>
  <div slot="message">Your changes have been saved.</div>
</div>

<!-- Error alert -->
<div data-include=".components/alert.html" data-type="error">
  <div slot="title">Error</div>
  <div slot="message">Something went wrong. Please try again.</div>
</div>
```

## Component Organization

### Directory Structure

Organize components logically:

```
src/
├── .components/
│   ├── layout/
│   │   ├── header.html
│   │   ├── footer.html
│   │   ├── sidebar.html
│   │   └── layout.html
│   ├── ui/
│   │   ├── button.html
│   │   ├── card.html
│   │   ├── modal.html
│   │   └── alert.html
│   ├── content/
│   │   ├── blog-post.html
│   │   ├── product-grid.html
│   │   └── testimonial.html
│   └── forms/
│       ├── contact-form.html
│       ├── newsletter.html
│       └── search.html
```

### Component Documentation

Document components within the files:

```html
<!-- 
  Button Component
  
  A flexible button component with various styles and states.
  
  Slots:
  - content: Button text or content (required)
  - icon: Optional icon before text
  
  Attributes:
  - data-style: primary, secondary, outline (default: primary)
  - data-size: small, medium, large (default: medium)
  - data-loading: true/false (default: false)
  
  Example:
  <div data-include=".components/ui/button.html" data-style="primary" data-size="large">
    <div slot="content">Click Me</div>
  </div>
-->
<button class="btn" 
        data-style="primary" 
        data-size="medium"
        data-loading="false">
  <span class="btn-icon">
    <slot name="icon">
      <!-- Optional icon -->
    </slot>
  </span>
  
  <span class="btn-content">
    <slot name="content">
      Button Text
    </slot>
  </span>
  
  <span class="btn-loading" style="display: none;">
    <svg class="spinner"><!-- loading spinner --></svg>
  </span>
</button>
```

## Advanced Slot Techniques

### Named Slots with Fallbacks

Provide meaningful defaults for optional content:

```html
<!-- Navigation Component -->
<nav class="navigation">
  <div class="nav-brand">
    <slot name="brand">
      <a href="/">
        <img src="/default-logo.png" alt="Site Logo">
      </a>
    </slot>
  </div>
  
  <ul class="nav-links">
    <slot name="links">
      <li><a href="/">Home</a></li>
      <li><a href="/about">About</a></li>
    </slot>
  </ul>
  
  <div class="nav-actions">
    <slot name="actions">
      <button class="nav-toggle">Menu</button>
    </slot>
  </div>
</nav>
```

### Slot Composition Patterns

Combine multiple slots for complex layouts:

```html
<!-- Article Component -->
<article class="article">
  <header class="article-header">
    <div class="article-meta">
      <slot name="meta">
        <time>Today</time>
      </slot>
    </div>
    
    <h1 class="article-title">
      <slot name="title">
        Default Article Title
      </slot>
    </h1>
    
    <div class="article-subtitle">
      <slot name="subtitle">
        <!-- Optional subtitle -->
      </slot>
    </div>
  </header>
  
  <div class="article-featured">
    <slot name="featured">
      <!-- Optional featured image/video -->
    </slot>
  </div>
  
  <div class="article-content">
    <slot name="content">
      <p>Default article content.</p>
    </slot>
  </div>
  
  <footer class="article-footer">
    <div class="article-tags">
      <slot name="tags">
        <!-- Optional tags -->
      </slot>
    </div>
    
    <div class="article-actions">
      <slot name="actions">
        <button>Share</button>
      </slot>
    </div>
  </footer>
</article>
```

## Component State Management

### Data Attributes for State

Use data attributes to manage component state:

```html
<!-- Toggle Component -->
<div class="toggle" data-state="off">
  <input type="checkbox" class="toggle-input" onchange="toggleState(this)">
  
  <div class="toggle-track">
    <div class="toggle-thumb"></div>
  </div>
  
  <label class="toggle-label">
    <span class="toggle-label-off">
      <slot name="off-label">Off</slot>
    </span>
    <span class="toggle-label-on">
      <slot name="on-label">On</slot>
    </span>
  </label>
</div>

<script>
function toggleState(input) {
  const toggle = input.closest('.toggle');
  const isChecked = input.checked;
  toggle.setAttribute('data-state', isChecked ? 'on' : 'off');
}
</script>

<style>
.toggle[data-state="off"] .toggle-label-off { display: inline; }
.toggle[data-state="off"] .toggle-label-on { display: none; }
.toggle[data-state="on"] .toggle-label-off { display: none; }
.toggle[data-state="on"] .toggle-label-on { display: inline; }
</style>
```

### Event Handling in Components

Create interactive components with clean event handling:

```html
<!-- Accordion Component -->
<div class="accordion" data-multiple="false">
  <div class="accordion-item" data-expanded="false">
    <button class="accordion-trigger" onclick="toggleAccordion(this)">
      <span class="accordion-title">
        <slot name="title">Accordion Title</slot>
      </span>
      <span class="accordion-icon">▼</span>
    </button>
    
    <div class="accordion-content">
      <div class="accordion-content-inner">
        <slot name="content">
          <p>Accordion content goes here.</p>
        </slot>
      </div>
    </div>
  </div>
</div>

<script>
function toggleAccordion(trigger) {
  const item = trigger.closest('.accordion-item');
  const accordion = trigger.closest('.accordion');
  const isExpanded = item.getAttribute('data-expanded') === 'true';
  
  // Handle single vs multiple expansion
  if (accordion.getAttribute('data-multiple') === 'false') {
    // Close all other items
    accordion.querySelectorAll('.accordion-item').forEach(otherItem => {
      if (otherItem !== item) {
        otherItem.setAttribute('data-expanded', 'false');
      }
    });
  }
  
  // Toggle current item
  item.setAttribute('data-expanded', (!isExpanded).toString());
}
</script>
```

## Performance Optimization

### Component Lazy Loading

Load components only when needed:

```html
<!-- Lazy loaded component -->
<div class="lazy-component" 
     data-component=".components/heavy-component.html"
     data-trigger="scroll">
  <div class="placeholder">
    Loading component...
  </div>
</div>

<script>
document.addEventListener('DOMContentLoaded', function() {
  const lazyComponents = document.querySelectorAll('.lazy-component');
  
  const observer = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        const element = entry.target;
        const componentPath = element.getAttribute('data-component');
        
        fetch(componentPath)
          .then(response => response.text())
          .then(html => {
            element.innerHTML = html;
            element.classList.add('loaded');
          })
          .catch(error => {
            console.error('Error loading component:', error);
          });
        
        observer.unobserve(element);
      }
    });
  });
  
  lazyComponents.forEach(component => {
    observer.observe(component);
  });
});
</script>
```

### Component Caching

Cache frequently used components:

```javascript
// Component cache for better performance
class ComponentCache {
  constructor() {
    this.cache = new Map();
  }
  
  async getComponent(path) {
    if (this.cache.has(path)) {
      return this.cache.get(path);
    }
    
    try {
      const response = await fetch(path);
      const html = await response.text();
      this.cache.set(path, html);
      return html;
    } catch (error) {
      console.error('Failed to load component:', path, error);
      return null;
    }
  }
  
  clearCache() {
    this.cache.clear();
  }
}

const componentCache = new ComponentCache();
```

## Testing Components

### Component Testing Strategy

Test components in isolation:

```html
<!-- test/components/button.test.html -->
<!DOCTYPE html>
<html>
<head>
  <title>Button Component Test</title>
  <link rel="stylesheet" href="../../src/styles.css">
</head>
<body>
  <h1>Button Component Tests</h1>
  
  <section>
    <h2>Default Button</h2>
    <div data-include="../../src/.components/ui/button.html">
      <div slot="content">Default Button</div>
    </div>
  </section>
  
  <section>
    <h2>Primary Button</h2>
    <div data-include="../../src/.components/ui/button.html" data-style="primary">
      <div slot="content">Primary Button</div>
    </div>
  </section>
  
  <section>
    <h2>Button with Icon</h2>
    <div data-include="../../src/.components/ui/button.html" data-style="primary">
      <div slot="icon">🚀</div>
      <div slot="content">Launch</div>
    </div>
  </section>
  
  <script>
    // Test component functionality
    document.addEventListener('DOMContentLoaded', function() {
      const buttons = document.querySelectorAll('.btn');
      
      buttons.forEach(button => {
        button.addEventListener('click', function() {
          console.log('Button clicked:', this.textContent);
        });
      });
      
      console.log(`Loaded ${buttons.length} button components`);
    });
  </script>
</body>
</html>
```

### Automated Component Testing

Create test scripts for component validation:

```javascript
// test/component-tests.js
class ComponentTester {
  constructor() {
    this.results = [];
  }
  
  async testComponent(componentPath, testCases) {
    for (const testCase of testCases) {
      try {
        const result = await this.runTest(componentPath, testCase);
        this.results.push({
          component: componentPath,
          test: testCase.name,
          passed: result.passed,
          message: result.message
        });
      } catch (error) {
        this.results.push({
          component: componentPath,
          test: testCase.name,
          passed: false,
          message: error.message
        });
      }
    }
  }
  
  async runTest(componentPath, testCase) {
    // Load component
    const response = await fetch(componentPath);
    const html = await response.text();
    
    // Validate structure
    const parser = new DOMParser();
    const doc = parser.parseFromString(html, 'text/html');
    
    // Run test case
    return testCase.validate(doc);
  }
  
  generateReport() {
    const passed = this.results.filter(r => r.passed).length;
    const total = this.results.length;
    
    console.log(`Component Tests: ${passed}/${total} passed`);
    
    this.results.forEach(result => {
      const status = result.passed ? '✅' : '❌';
      console.log(`${status} ${result.component} - ${result.test}: ${result.message}`);
    });
  }
}
```

## Best Practices

### Component Design Principles

1. **Single Responsibility**: Each component should have one clear purpose
2. **Composability**: Components should work well together
3. **Flexibility**: Use slots for customizable content areas
4. **Accessibility**: Include proper ARIA attributes and semantic HTML
5. **Performance**: Minimize CSS and JavaScript within components

### Naming Conventions

```
.components/
├── layout/          # Page structure components
├── ui/              # User interface elements
├── content/         # Content-specific components
├── forms/           # Form-related components
└── vendor/          # Third-party integrations
```

### Documentation Standards

Document each component with:
- Purpose and use cases
- Available slots and their purpose
- Data attributes and their effects
- Dependencies and requirements
- Usage examples

### Error Handling

Provide graceful fallbacks for missing content:

```html
<!-- Robust component with error handling -->
<div class="image-gallery" data-images="[]">
  <div class="gallery-container">
    <slot name="images">
      <div class="gallery-placeholder">
        <p>No images available</p>
        <button onclick="loadDefaultImages()">Load Sample Images</button>
      </div>
    </slot>
  </div>
  
  <div class="gallery-controls">
    <slot name="controls">
      <button onclick="previousImage()">Previous</button>
      <button onclick="nextImage()">Next</button>
    </slot>
  </div>
</div>
```

## Migration and Refactoring

### Converting Static HTML to Components

Transform repetitive HTML patterns into reusable components:

**Before (repetitive)**:
```html
<div class="product-card">
  <img src="/product1.jpg" alt="Product 1">
  <h3>Product 1</h3>
  <p>$99.99</p>
  <button>Add to Cart</button>
</div>

<div class="product-card">
  <img src="/product2.jpg" alt="Product 2">
  <h3>Product 2</h3>
  <p>$149.99</p>
  <button>Add to Cart</button>
</div>
```

**After (component-based)**:
```html
<!-- Product card component -->
<div data-include=".components/product-card.html">
  <div slot="image"><img src="/product1.jpg" alt="Product 1"></div>
  <div slot="title">Product 1</div>
  <div slot="price">$99.99</div>
</div>

<div data-include=".components/product-card.html">
  <div slot="image"><img src="/product2.jpg" alt="Product 2"></div>
  <div slot="title">Product 2</div>
  <div slot="price">$149.99</div>
</div>
```

### Component Refactoring Checklist

- [ ] Identify repeated HTML patterns
- [ ] Extract common structure into components
- [ ] Define clear slot boundaries
- [ ] Add proper documentation
- [ ] Test component variations
- [ ] Update existing pages to use components
- [ ] Verify build output matches original

## Next Steps

### Advanced Topics
- [Slot and Template Injection: Best Practices](slot-template-injection.md)
- [Building Component Libraries](component-libraries.md)
- [Performance Optimization](../guides/performance-optimization.md)

### Integration Examples
- [Component-Based Design Systems](../examples/design-systems.md)
- [Third-Party Component Integration](../integrations/third-party.md)
- [Headless CMS with Components](../integrations/headless-cms.md)

### Community Resources
- [Component Gallery](https://github.com/fwdslsh/unify-components)
- [Best Practices Discussion](https://github.com/orgs/fwdslsh/discussions)
- [Contributing Components](../contributing/contributing.md)

**You're now ready to build sophisticated, maintainable component-based websites with Unify!** 🎨

---

*This guide references the [Unify documentation](https://github.com/fwdslsh/unify) and is part of the [fwdslsh examples repository](https://github.com/fwdslsh/examples). For the latest component patterns and examples, visit [fwdslsh.dev](https://fwdslsh.dev).*