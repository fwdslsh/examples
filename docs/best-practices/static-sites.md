# Static Site Best Practices

This guide covers best practices for building fast, maintainable, and SEO-friendly static sites with modern tools like Unify.

## 🏗️ Site Architecture

### Component-Based Structure

Organize your site using reusable components:

```
src/
├── .components/
│   ├── layout/           # Page layouts
│   │   ├── base.html
│   │   ├── docs.html
│   │   └── blog.html
│   ├── ui/               # UI components  
│   │   ├── button.html
│   │   ├── card.html
│   │   └── modal.html
│   ├── sections/         # Page sections
│   │   ├── header.html
│   │   ├── footer.html
│   │   └── sidebar.html
│   └── forms/            # Form components
│       ├── contact.html
│       └── newsletter.html
├── pages/                # Individual pages
├── assets/               # Static assets
│   ├── css/
│   ├── js/
│   ├── images/
│   └── fonts/
└── data/                 # Site data files
    ├── navigation.json
    └── config.json
```

### Naming Conventions

Use consistent, descriptive naming:

```html
<!-- Components: Use kebab-case -->
<!--#include virtual="/.components/ui/call-to-action.html" -->
<!--#include virtual="/.components/layout/blog-post.html" -->

<!-- CSS classes: Use BEM methodology -->
<div class="card">
  <h2 class="card__title">Title</h2>
  <p class="card__content">Content</p>
  <button class="card__button card__button--primary">Action</button>
</div>

<!-- File names: Use descriptive, hyphenated names -->
getting-started.html
api-reference.html
privacy-policy.html
```

## 🎨 CSS Organization

### Modern CSS Architecture

Use a scalable CSS structure:

```css
/* 1. CSS Custom Properties (Variables) */
:root {
  /* Colors */
  --color-primary: #3b82f6;
  --color-secondary: #6b7280;
  --color-text: #1f2937;
  --color-background: #ffffff;
  
  /* Typography */
  --font-family-primary: 'Inter', system-ui, sans-serif;
  --font-family-mono: 'JetBrains Mono', monospace;
  --font-size-base: 1rem;
  --line-height-base: 1.6;
  
  /* Spacing */
  --spacing-xs: 0.25rem;
  --spacing-sm: 0.5rem;
  --spacing-md: 1rem;
  --spacing-lg: 2rem;
  --spacing-xl: 4rem;
  
  /* Layout */
  --container-max-width: 1200px;
  --header-height: 64px;
  --sidebar-width: 280px;
}

/* 2. Reset and Base Styles */
*,
*::before,
*::after {
  box-sizing: border-box;
}

body {
  margin: 0;
  font-family: var(--font-family-primary);
  font-size: var(--font-size-base);
  line-height: var(--line-height-base);
  color: var(--color-text);
  background-color: var(--color-background);
}

/* 3. Layout Components */
.container {
  max-width: var(--container-max-width);
  margin: 0 auto;
  padding: 0 var(--spacing-md);
}

.grid {
  display: grid;
  gap: var(--spacing-md);
}

.flex {
  display: flex;
  gap: var(--spacing-md);
}

/* 4. Component Styles */
.button {
  display: inline-flex;
  align-items: center;
  padding: var(--spacing-sm) var(--spacing-md);
  border: none;
  border-radius: 6px;
  font-weight: 500;
  text-decoration: none;
  cursor: pointer;
  transition: all 0.2s ease;
}

.button--primary {
  background-color: var(--color-primary);
  color: white;
}

.button--primary:hover {
  background-color: var(--color-primary-dark);
}
```

### CSS Organization Structure

```
assets/css/
├── base/
│   ├── reset.css          # CSS reset
│   ├── typography.css     # Font and text styles
│   └── variables.css      # CSS custom properties
├── components/
│   ├── buttons.css        # Button styles
│   ├── cards.css          # Card components
│   ├── forms.css          # Form elements
│   └── navigation.css     # Navigation styles
├── layout/
│   ├── grid.css           # Grid system
│   ├── header.css         # Header layout
│   └── sidebar.css        # Sidebar layout
├── pages/
│   ├── home.css           # Homepage specific
│   ├── blog.css           # Blog specific
│   └── docs.css           # Documentation specific
├── utilities/
│   ├── spacing.css        # Margin/padding utilities
│   ├── display.css        # Display utilities
│   └── colors.css         # Color utilities
└── main.css               # Main CSS file (imports all)
```

### CSS Best Practices

1. **Use CSS Custom Properties for theming:**
   ```css
   /* Light theme */
   :root {
     --bg-primary: #ffffff;
     --text-primary: #1f2937;
   }
   
   /* Dark theme */
   [data-theme="dark"] {
     --bg-primary: #1f2937;
     --text-primary: #f9fafb;
   }
   ```

2. **Prefer CSS Grid and Flexbox for layout:**
   ```css
   .layout {
     display: grid;
     grid-template-columns: var(--sidebar-width) 1fr;
     grid-template-rows: var(--header-height) 1fr;
     grid-template-areas: 
       "sidebar header"
       "sidebar main";
   }
   
   .sidebar { grid-area: sidebar; }
   .header { grid-area: header; }
   .main { grid-area: main; }
   ```

3. **Use logical properties for internationalization:**
   ```css
   .content {
     margin-inline-start: var(--spacing-md);
     padding-block: var(--spacing-lg);
     border-inline-end: 1px solid var(--border-color);
   }
   ```

## 📱 Responsive Design

### Mobile-First Approach

Design for mobile first, then enhance for larger screens:

```css
/* Mobile styles (default) */
.grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: var(--spacing-md);
}

.navigation {
  display: none; /* Hidden on mobile */
}

.mobile-menu {
  display: block;
}

/* Tablet and up */
@media (min-width: 768px) {
  .grid {
    grid-template-columns: repeat(2, 1fr);
  }
  
  .navigation {
    display: flex;
  }
  
  .mobile-menu {
    display: none;
  }
}

/* Desktop and up */
@media (min-width: 1024px) {
  .grid {
    grid-template-columns: repeat(3, 1fr);
  }
  
  .container {
    padding: 0 var(--spacing-xl);
  }
}
```

### Responsive Typography

Use fluid typography that scales with screen size:

```css
:root {
  /* Fluid typography */
  --font-size-sm: clamp(0.875rem, 0.8rem + 0.375vw, 1rem);
  --font-size-base: clamp(1rem, 0.9rem + 0.5vw, 1.125rem);
  --font-size-lg: clamp(1.125rem, 1rem + 0.625vw, 1.5rem);
  --font-size-xl: clamp(1.5rem, 1.3rem + 1vw, 2.25rem);
  --font-size-2xl: clamp(2rem, 1.6rem + 2vw, 3.5rem);
}

h1 { font-size: var(--font-size-2xl); }
h2 { font-size: var(--font-size-xl); }
h3 { font-size: var(--font-size-lg); }
p  { font-size: var(--font-size-base); }
```

### Container Queries (Modern Approach)

Use container queries for truly responsive components:

```css
.card-container {
  container-type: inline-size;
}

.card {
  display: grid;
  grid-template-columns: 1fr;
  gap: var(--spacing-md);
}

/* When container is wider than 300px */
@container (min-width: 300px) {
  .card {
    grid-template-columns: auto 1fr;
  }
}

/* When container is wider than 500px */
@container (min-width: 500px) {
  .card {
    grid-template-columns: 1fr 1fr 1fr;
  }
}
```

## ⚡ Performance Optimization

### Image Optimization

1. **Use modern image formats:**
   ```html
   <picture>
     <source srcset="image.avif" type="image/avif">
     <source srcset="image.webp" type="image/webp">
     <img src="image.jpg" alt="Description" loading="lazy">
   </picture>
   ```

2. **Implement responsive images:**
   ```html
   <img 
     src="image-400.jpg"
     srcset="image-400.jpg 400w, 
             image-800.jpg 800w, 
             image-1200.jpg 1200w"
     sizes="(max-width: 768px) 400px, 
            (max-width: 1024px) 800px, 
            1200px"
     alt="Description"
     loading="lazy"
   >
   ```

3. **Use CSS for decorative images:**
   ```css
   .hero {
     background-image: 
       image-set(
         "hero.avif" type("image/avif"),
         "hero.webp" type("image/webp"),
         "hero.jpg" type("image/jpeg")
       );
   }
   ```

### CSS Performance

1. **Minimize CSS bundle size:**
   ```css
   /* Use CSS custom properties to reduce repetition */
   :root {
     --shadow-base: 0 1px 3px 0 rgb(0 0 0 / 0.1);
   }
   
   .card, .modal, .dropdown {
     box-shadow: var(--shadow-base);
   }
   ```

2. **Optimize critical CSS:**
   ```html
   <!-- Inline critical CSS -->
   <style>
     /* Critical above-the-fold styles */
     body { font-family: system-ui; }
     .header { height: 64px; }
   </style>
   
   <!-- Load non-critical CSS asynchronously -->
   <link rel="preload" href="/css/main.css" as="style" onload="this.onload=null;this.rel='stylesheet'">
   <noscript><link rel="stylesheet" href="/css/main.css"></noscript>
   ```

3. **Use CSS containment:**
   ```css
   .sidebar {
     contain: layout style paint;
   }
   
   .card {
     contain: layout paint;
   }
   ```

### JavaScript Performance

1. **Use modern JavaScript features:**
   ```javascript
   // Use native APIs when possible
   const observer = new IntersectionObserver((entries) => {
     entries.forEach(entry => {
       if (entry.isIntersecting) {
         entry.target.classList.add('visible');
       }
     });
   });
   
   // Observe lazy-loaded elements
   document.querySelectorAll('[data-lazy]').forEach(el => {
     observer.observe(el);
   });
   ```

2. **Implement code splitting:**
   ```html
   <!-- Load JavaScript modules conditionally -->
   <script type="module">
     if ('serviceWorker' in navigator) {
       import('./js/sw-register.js');
     }
     
     if (window.innerWidth > 768) {
       import('./js/desktop-features.js');
     }
   </script>
   ```

3. **Use efficient event handling:**
   ```javascript
   // Use event delegation instead of individual listeners
   document.addEventListener('click', (event) => {
     if (event.target.matches('[data-toggle]')) {
       handleToggle(event.target);
     }
     
     if (event.target.matches('[data-modal-open]')) {
       openModal(event.target.dataset.modalTarget);
     }
   });
   ```

## 🔍 SEO Optimization

### Meta Tags and Structured Data

1. **Essential meta tags:**
   ```html
   <head>
     <!-- Essential meta tags -->
     <meta charset="UTF-8">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     <title>Page Title | Site Name</title>
     <meta name="description" content="Concise page description under 160 characters">
     
     <!-- Open Graph for social sharing -->
     <meta property="og:title" content="Page Title">
     <meta property="og:description" content="Page description">
     <meta property="og:image" content="/images/social-share.jpg">
     <meta property="og:url" content="https://yourdomain.com/current-page">
     <meta property="og:type" content="website">
     
     <!-- Twitter Card -->
     <meta name="twitter:card" content="summary_large_image">
     <meta name="twitter:title" content="Page Title">
     <meta name="twitter:description" content="Page description">
     <meta name="twitter:image" content="/images/social-share.jpg">
     
     <!-- Canonical URL -->
     <link rel="canonical" href="https://yourdomain.com/current-page">
   </head>
   ```

2. **Structured data for rich snippets:**
   ```html
   <script type="application/ld+json">
   {
     "@context": "https://schema.org",
     "@type": "Article",
     "headline": "Article Title",
     "author": {
       "@type": "Person",
       "name": "Author Name"
     },
     "datePublished": "2024-01-15",
     "dateModified": "2024-01-16",
     "description": "Article description",
     "image": "/images/article-image.jpg"
   }
   </script>
   ```

### Content Structure

1. **Use semantic HTML:**
   ```html
   <article>
     <header>
       <h1>Article Title</h1>
       <time datetime="2024-01-15">January 15, 2024</time>
     </header>
     
     <section>
       <h2>Section Heading</h2>
       <p>Content paragraph...</p>
     </section>
     
     <aside>
       <h3>Related Articles</h3>
       <nav aria-label="Related articles">
         <ul>
           <li><a href="/related-1">Related Article 1</a></li>
           <li><a href="/related-2">Related Article 2</a></li>
         </ul>
       </nav>
     </aside>
   </article>
   ```

2. **Optimize heading hierarchy:**
   ```html
   <!-- Good heading structure -->
   <h1>Main Page Title</h1>
     <h2>Section 1</h2>
       <h3>Subsection 1.1</h3>
       <h3>Subsection 1.2</h3>
     <h2>Section 2</h2>
       <h3>Subsection 2.1</h3>
   ```

3. **Internal linking strategy:**
   ```html
   <!-- Use descriptive anchor text -->
   <p>Learn more about <a href="/seo-guide">SEO optimization techniques</a> 
   to improve your site's search rankings.</p>
   
   <!-- Add rel="noopener" for external links -->
   <a href="https://external-site.com" target="_blank" rel="noopener">
     External Resource
   </a>
   ```

## ♿ Accessibility

### ARIA and Semantic HTML

1. **Use proper ARIA labels:**
   ```html
   <button aria-expanded="false" aria-controls="mobile-menu" aria-label="Toggle navigation menu">
     <span class="hamburger-icon"></span>
   </button>
   
   <nav id="mobile-menu" aria-hidden="true">
     <ul role="list">
       <li><a href="/" aria-current="page">Home</a></li>
       <li><a href="/about">About</a></li>
       <li><a href="/contact">Contact</a></li>
     </ul>
   </nav>
   ```

2. **Ensure keyboard navigation:**
   ```css
   /* Focus styles */
   *:focus {
     outline: 2px solid var(--color-primary);
     outline-offset: 2px;
   }
   
   /* Skip link for keyboard users */
   .skip-link {
     position: absolute;
     top: -40px;
     left: 6px;
     background: var(--color-primary);
     color: white;
     padding: 8px;
     text-decoration: none;
     transition: top 0.3s;
   }
   
   .skip-link:focus {
     top: 6px;
   }
   ```

3. **Alt text for images:**
   ```html
   <!-- Informative images -->
   <img src="chart.jpg" alt="Sales increased 25% from Q1 to Q2 2024">
   
   <!-- Decorative images -->
   <img src="decoration.jpg" alt="" role="presentation">
   
   <!-- Complex images -->
   <figure>
     <img src="complex-chart.jpg" alt="Quarterly sales data visualization">
     <figcaption>
       Detailed description: Sales in Q1 were $100k, Q2 $125k, 
       representing a 25% increase...
     </figcaption>
   </figure>
   ```

### Color and Contrast

1. **Ensure sufficient contrast:**
   ```css
   :root {
     /* WCAG AA compliant color combinations */
     --text-on-light: #1f2937;    /* 16.74:1 contrast ratio */
     --text-on-primary: #ffffff;  /* 4.52:1 contrast ratio */
     --link-color: #1d4ed8;       /* 7.04:1 contrast ratio */
   }
   ```

2. **Don't rely solely on color:**
   ```html
   <!-- Good: Uses both color and icon -->
   <div class="alert alert--error">
     <svg class="alert__icon" aria-hidden="true">
       <use href="#error-icon"></use>
     </svg>
     <span>Error: Please fill in all required fields</span>
   </div>
   ```

## 🛠️ Build Process Optimization

### Asset Pipeline

1. **Optimize build output:**
   ```javascript
   // Build configuration example
   const buildConfig = {
     // Minify HTML
     minifyHTML: true,
     
     // Optimize CSS
     optimizeCSS: {
       removeUnused: true,
       minify: true,
       autoprefixer: true
     },
     
     // Compress images
     optimizeImages: {
       formats: ['webp', 'avif'],
       quality: 85,
       progressive: true
     },
     
     // Bundle JavaScript
     bundleJS: {
       minify: true,
       sourceMaps: process.env.NODE_ENV === 'development'
     }
   };
   ```

2. **Generate critical resources:**
   ```bash
   # Generate sitemap
   unify build --generate-sitemap
   
   # Generate RSS feed
   unify build --generate-rss
   
   # Generate service worker
   unify build --generate-sw
   ```

### Caching Strategy

1. **Implement proper caching headers:**
   ```
   # .htaccess for Apache
   <IfModule mod_expires.c>
     ExpiresActive on
     ExpiresByType text/css "access plus 1 year"
     ExpiresByType application/javascript "access plus 1 year"
     ExpiresByType image/png "access plus 1 year"
     ExpiresByType image/jpg "access plus 1 year"
     ExpiresByType image/jpeg "access plus 1 year"
     ExpiresByType image/webp "access plus 1 year"
     ExpiresByType text/html "access plus 0 seconds"
   </IfModule>
   ```

2. **Use content-based filenames:**
   ```javascript
   // Build process should generate hashed filenames
   main.css → main.a1b2c3d4.css
   app.js → app.e5f6g7h8.js
   ```

## 📊 Analytics and Monitoring

### Performance Monitoring

1. **Core Web Vitals tracking:**
   ```javascript
   // Track Core Web Vitals
   import {getCLS, getFID, getFCP, getLCP, getTTFB} from 'web-vitals';

   function sendToAnalytics(metric) {
     // Send to your analytics service
     gtag('event', metric.name, {
       value: Math.round(metric.value),
       event_category: 'Web Vitals',
       non_interaction: true,
     });
   }

   getCLS(sendToAnalytics);
   getFID(sendToAnalytics);
   getFCP(sendToAnalytics);
   getLCP(sendToAnalytics);
   getTTFB(sendToAnalytics);
   ```

2. **Error tracking:**
   ```javascript
   // Global error handler
   window.addEventListener('error', (event) => {
     console.error('Global error:', event.error);
     // Send to error tracking service
   });
   
   window.addEventListener('unhandledrejection', (event) => {
     console.error('Unhandled promise rejection:', event.reason);
     // Send to error tracking service
   });
   ```

### Privacy-Friendly Analytics

1. **Use privacy-focused solutions:**
   ```html
   <!-- Plausible Analytics (privacy-friendly) -->
   <script defer data-domain="yourdomain.com" src="https://plausible.io/js/plausible.js"></script>
   
   <!-- Or implement simple analytics -->
   <script>
     // Simple page view tracking
     fetch('/api/analytics', {
       method: 'POST',
       headers: { 'Content-Type': 'application/json' },
       body: JSON.stringify({
         page: window.location.pathname,
         referrer: document.referrer,
         timestamp: Date.now()
       })
     });
   </script>
   ```

## 🔒 Security Best Practices

### Content Security Policy

```html
<meta http-equiv="Content-Security-Policy" content="
  default-src 'self';
  script-src 'self' 'unsafe-inline' https://plausible.io;
  style-src 'self' 'unsafe-inline' https://fonts.googleapis.com;
  font-src 'self' https://fonts.gstatic.com;
  img-src 'self' data: https:;
  connect-src 'self' https://api.yourdomain.com;
">
```

### Secure Headers

```html
<!-- Prevent clickjacking -->
<meta http-equiv="X-Frame-Options" content="DENY">

<!-- Prevent MIME type sniffing -->
<meta http-equiv="X-Content-Type-Options" content="nosniff">

<!-- Referrer policy -->
<meta name="referrer" content="strict-origin-when-cross-origin">
```

## 📝 Documentation and Maintenance

### Code Documentation

1. **Document component APIs:**
   ```html
   <!--
     Button Component
     
     Usage:
     <!--#set var="button_text" value="Click me" -->
     <!--#set var="button_type" value="primary" -->
     <!--#set var="button_url" value="/contact" -->
     <!--#include virtual="/.components/ui/button.html" -->
     
     Variables:
     - button_text (required): Button label
     - button_type (optional): primary, secondary, danger
     - button_url (optional): Link URL, creates <a> instead of <button>
   -->
   ```

2. **Maintain a style guide:**
   ```markdown
   # Component Style Guide
   
   ## Colors
   - Primary: #3b82f6 (Blue 500)
   - Secondary: #6b7280 (Gray 500)
   - Success: #10b981 (Emerald 500)
   
   ## Typography
   - Headers: Inter, 600 weight
   - Body: Inter, 400 weight
   - Code: JetBrains Mono, 400 weight
   
   ## Spacing Scale
   - xs: 4px
   - sm: 8px
   - md: 16px
   - lg: 32px
   - xl: 64px
   ```

### Performance Budget

Set and monitor performance budgets:

```json
{
  "performanceBudget": {
    "maxFileSize": {
      "css": 50,
      "js": 100,
      "images": 200
    },
    "maxTotalSize": 1000,
    "maxRequests": 50,
    "timing": {
      "firstContentfulPaint": 1500,
      "largestContentfulPaint": 2500,
      "cumulativeLayoutShift": 0.1
    }
  }
}
```

Following these best practices will help you build static sites that are fast, accessible, maintainable, and provide an excellent user experience across all devices and browsers. 🚀