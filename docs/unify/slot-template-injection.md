# Slot and Template Injection: Best Practices

Master advanced layout patterns and slot management in Unify for complex, maintainable web applications. This comprehensive guide covers sophisticated templating techniques for senior developers and technical architects.

## Overview

Unify's slot and template injection system provides:
- **Hierarchical slot inheritance** for complex layout nesting
- **Dynamic slot resolution** with runtime content injection
- **Template composition patterns** for scalable architectures
- **Conditional slot rendering** based on content availability
- **Performance-optimized** slot compilation and caching

## Advanced Slot Patterns

### Hierarchical Slot Inheritance

Create nested layout systems with inherited slots:

**`src/.components/layouts/base.html`**:
```html
<!-- Base Layout Template -->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  
  <!-- Meta slot with inheritance -->
  <slot name="meta">
    <title><slot name="title">Default Site Title</slot></title>
    <meta name="description" content="Default description">
  </slot>
  
  <!-- Head resources with composition -->
  <slot name="head-resources">
    <link rel="stylesheet" href="/styles.css">
    <slot name="styles">
      <!-- Additional styles -->
    </slot>
  </slot>
</head>
<body class="<slot name='body-class'>default-layout</slot>">
  <!-- Layout structure with nested slots -->
  <div class="layout-container">
    <slot name="layout-content">
      <!-- Default layout structure -->
      <header class="site-header">
        <slot name="header">
          <h1>Default Header</h1>
        </slot>
      </header>
      
      <main class="site-main">
        <slot name="main">
          <p>Default main content</p>
        </slot>
      </main>
      
      <footer class="site-footer">
        <slot name="footer">
          <p>Default footer</p>
        </slot>
      </footer>
    </slot>
  </div>
  
  <!-- Scripts with composition -->
  <slot name="scripts">
    <script src="/scripts.js"></script>
    <slot name="additional-scripts">
      <!-- Page-specific scripts -->
    </slot>
  </slot>
</body>
</html>
```

**`src/.components/layouts/article.html`**:
```html
<!-- Article Layout extending Base Layout -->
<div data-include="layouts/base.html">
  <!-- Inherit and extend meta information -->
  <div slot="meta">
    <title><slot name="article-title">Article Title</slot> | My Blog</title>
    <meta name="description" content="<slot name='article-description'>Article description</slot>">
    <meta property="og:title" content="<slot name='article-title'>Article Title</slot>">
    <meta property="og:type" content="article">
    <meta property="article:author" content="<slot name='article-author'>Author Name</slot>">
    <meta property="article:published_time" content="<slot name='article-date'>2024-01-01</slot>">
  </div>
  
  <!-- Add article-specific styles -->
  <div slot="styles">
    <link rel="stylesheet" href="/article.css">
    <slot name="article-styles">
      <!-- Article-specific styles -->
    </slot>
  </div>
  
  <!-- Set body class for article layout -->
  <div slot="body-class">article-layout <slot name="article-class">standard-article</slot></div>
  
  <!-- Override layout content with article structure -->
  <div slot="layout-content">
    <!-- Article header -->
    <header class="article-header">
      <div class="container">
        <nav class="breadcrumb">
          <slot name="breadcrumb">
            <a href="/">Home</a> > 
            <a href="/blog">Blog</a> > 
            <span><slot name="article-title">Article</slot></span>
          </slot>
        </nav>
        
        <div class="article-meta">
          <h1 class="article-title">
            <slot name="article-title">Article Title</slot>
          </h1>
          
          <div class="article-byline">
            <slot name="byline">
              By <span class="author"><slot name="article-author">Author</slot></span>
              on <time class="date"><slot name="article-date">Date</slot></time>
            </slot>
          </div>
          
          <div class="article-tags">
            <slot name="tags">
              <!-- Article tags -->
            </slot>
          </div>
        </div>
      </div>
    </header>
    
    <!-- Article content -->
    <main class="article-main">
      <div class="container">
        <article class="article-content">
          <slot name="article-content">
            <p>Article content goes here.</p>
          </slot>
        </article>
        
        <aside class="article-sidebar">
          <slot name="sidebar">
            <!-- Related articles, author bio, etc. -->
          </slot>
        </aside>
      </div>
    </main>
    
    <!-- Article footer -->
    <footer class="article-footer">
      <div class="container">
        <slot name="article-footer">
          <div class="share-buttons">
            <slot name="share-buttons">
              <!-- Social sharing buttons -->
            </slot>
          </div>
          
          <div class="related-articles">
            <slot name="related-articles">
              <!-- Related articles -->
            </slot>
          </div>
        </slot>
      </div>
    </footer>
  </div>
  
  <!-- Add article-specific scripts -->
  <div slot="additional-scripts">
    <script src="/article.js"></script>
    <slot name="article-scripts">
      <!-- Article-specific JavaScript -->
    </slot>
  </div>
</div>
```

### Dynamic Slot Resolution

Implement runtime slot content based on data attributes:

**`src/.components/dynamic-card.html`**:
```html
<!-- Dynamic Card with Conditional Slots -->
<div class="dynamic-card" 
     data-card-type="default"
     data-has-media="false"
     data-has-actions="false">
  
  <!-- Header slot - always visible -->
  <header class="card-header">
    <slot name="header">
      <h3 class="card-title">
        <slot name="title">Default Title</slot>
      </h3>
      
      <!-- Conditional subtitle -->
      <div class="card-subtitle" data-slot-condition="subtitle">
        <slot name="subtitle">
          <!-- Subtitle only shown if content provided -->
        </slot>
      </div>
    </slot>
  </header>
  
  <!-- Conditional media slot -->
  <div class="card-media" data-slot-condition="media">
    <slot name="media">
      <!-- Media content -->
    </slot>
  </div>
  
  <!-- Main content slot -->
  <div class="card-content">
    <slot name="content">
      <p>Default card content.</p>
    </slot>
  </div>
  
  <!-- Conditional actions slot -->
  <footer class="card-actions" data-slot-condition="actions">
    <slot name="actions">
      <!-- Action buttons -->
    </slot>
  </footer>
</div>

<script>
// Dynamic slot management
document.addEventListener('DOMContentLoaded', function() {
  document.querySelectorAll('.dynamic-card').forEach(card => {
    // Check for conditional slots
    const conditionalSlots = card.querySelectorAll('[data-slot-condition]');
    
    conditionalSlots.forEach(element => {
      const condition = element.getAttribute('data-slot-condition');
      const slot = element.querySelector(`slot[name="${condition}"]`);
      
      if (slot) {
        // Check if slot has custom content
        const hasContent = slot.assignedNodes && slot.assignedNodes().length > 0;
        
        if (!hasContent) {
          // Hide empty conditional slots
          element.style.display = 'none';
          card.setAttribute(`data-has-${condition}`, 'false');
        } else {
          card.setAttribute(`data-has-${condition}`, 'true');
        }
      }
    });
  });
});
</script>

<style>
/* Conditional styling based on slot content */
.dynamic-card[data-has-media="true"] {
  /* Styles when media is present */
}

.dynamic-card[data-has-actions="false"] .card-content {
  /* Adjust content when no actions */
  padding-bottom: 1.5rem;
}

.dynamic-card[data-card-type="featured"] {
  /* Featured card styles */
  border: 2px solid #007bff;
}
</style>
```

### Template Composition Patterns

Create modular templates that compose together:

**`src/.components/templates/section.html`**:
```html
<!-- Reusable Section Template -->
<section class="content-section" 
         data-section-type="default"
         data-background="none">
  
  <!-- Section background handling -->
  <div class="section-background">
    <slot name="background">
      <!-- Optional background content -->
    </slot>
  </div>
  
  <!-- Section container with flexible width -->
  <div class="section-container" data-width="container">
    <!-- Section header with optional components -->
    <header class="section-header" data-slot-condition="header">
      <slot name="header">
        <!-- Section header content -->
      </slot>
    </header>
    
    <!-- Main section content -->
    <div class="section-content">
      <slot name="content">
        <p>Section content goes here.</p>
      </slot>
    </div>
    
    <!-- Optional section footer -->
    <footer class="section-footer" data-slot-condition="footer">
      <slot name="footer">
        <!-- Section footer content -->
      </slot>
    </footer>
  </div>
</section>
```

**Usage with composition**:
```html
<!-- Hero section with composed templates -->
<div data-include=".components/templates/section.html" 
     data-section-type="hero" 
     data-background="image">
  
  <div slot="background">
    <img src="/hero-bg.jpg" alt="Hero background" class="section-bg-image">
    <div class="section-bg-overlay"></div>
  </div>
  
  <div slot="content">
    <!-- Compose hero content using other components -->
    <div data-include=".components/ui/hero-content.html">
      <div slot="headline">
        <h1>Welcome to Our Platform</h1>
      </div>
      <div slot="subheadline">
        <p>Build amazing websites with zero configuration.</p>
      </div>
      <div slot="actions">
        <div data-include=".components/ui/button.html" data-style="primary" data-size="large">
          <div slot="content">Get Started</div>
        </div>
        <div data-include=".components/ui/button.html" data-style="outline" data-size="large">
          <div slot="content">Learn More</div>
        </div>
      </div>
    </div>
  </div>
</div>

<!-- Feature section with card grid -->
<div data-include=".components/templates/section.html" data-section-type="features">
  <div slot="header">
    <h2>Features</h2>
    <p>Everything you need to build modern websites.</p>
  </div>
  
  <div slot="content">
    <div data-include=".components/ui/card-grid.html" data-columns="3">
      <div slot="cards">
        <!-- Individual feature cards -->
        <div data-include=".components/ui/card.html">
          <div slot="header">
            <h3>Fast Building</h3>
          </div>
          <div slot="content">
            <p>Lightning-fast builds with Bun.</p>
          </div>
        </div>
        
        <div data-include=".components/ui/card.html">
          <div slot="header">
            <h3>Zero Config</h3>
          </div>
          <div slot="content">
            <p>No configuration required to get started.</p>
          </div>
        </div>
        
        <div data-include=".components/ui/card.html">
          <div slot="header">
            <h3>Component System</h3>
          </div>
          <div slot="content">
            <p>Reusable components with slots.</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>
```

## Advanced Slot Management

### Slot Validation and Error Handling

Implement robust slot validation:

**`src/.components/validated-form.html`**:
```html
<!-- Form Component with Slot Validation -->
<form class="validated-form" 
      data-validation="enabled"
      novalidate>
  
  <!-- Form header with validation -->
  <header class="form-header">
    <slot name="title" data-required="true">
      <h2>Form Title Required</h2>
    </slot>
    
    <div class="form-description">
      <slot name="description">
        <!-- Optional form description -->
      </slot>
    </div>
  </header>
  
  <!-- Form fields container -->
  <div class="form-fields">
    <slot name="fields" data-required="true">
      <p class="error">No form fields provided. Please add fields to the 'fields' slot.</p>
    </slot>
  </div>
  
  <!-- Form actions with default submit -->
  <footer class="form-footer">
    <slot name="actions">
      <button type="submit" class="btn btn-primary">Submit</button>
      <button type="reset" class="btn btn-secondary">Reset</button>
    </slot>
  </footer>
  
  <!-- Error messages container -->
  <div class="form-errors" style="display: none;">
    <slot name="errors">
      <!-- Dynamic error messages -->
    </slot>
  </div>
</form>

<script>
// Slot validation system
class SlotValidator {
  constructor(element) {
    this.element = element;
    this.errors = [];
  }
  
  validate() {
    this.errors = [];
    
    // Check required slots
    const requiredSlots = this.element.querySelectorAll('slot[data-required="true"]');
    
    requiredSlots.forEach(slot => {
      const slotName = slot.getAttribute('name');
      const hasContent = this.hasSlotContent(slot);
      
      if (!hasContent) {
        this.errors.push(`Required slot '${slotName}' is empty`);
        this.markSlotError(slot);
      }
    });
    
    // Validate slot content types
    this.validateSlotTypes();
    
    return this.errors.length === 0;
  }
  
  hasSlotContent(slot) {
    // Check if slot has been filled with content
    const slotName = slot.getAttribute('name');
    const slotContent = this.element.querySelector(`[slot="${slotName}"]`);
    
    return slotContent && slotContent.innerHTML.trim().length > 0;
  }
  
  validateSlotTypes() {
    // Add custom slot type validation
    const typedSlots = this.element.querySelectorAll('slot[data-type]');
    
    typedSlots.forEach(slot => {
      const expectedType = slot.getAttribute('data-type');
      const slotName = slot.getAttribute('name');
      const slotContent = this.element.querySelector(`[slot="${slotName}"]`);
      
      if (slotContent && !this.validateSlotType(slotContent, expectedType)) {
        this.errors.push(`Slot '${slotName}' content doesn't match expected type '${expectedType}'`);
      }
    });
  }
  
  validateSlotType(element, expectedType) {
    switch (expectedType) {
      case 'text':
        return element.textContent.trim().length > 0;
      case 'image':
        return element.querySelector('img') !== null;
      case 'form-field':
        return element.querySelector('input, select, textarea') !== null;
      default:
        return true;
    }
  }
  
  markSlotError(slot) {
    slot.classList.add('slot-error');
  }
  
  showErrors() {
    const errorContainer = this.element.querySelector('.form-errors');
    if (this.errors.length > 0) {
      errorContainer.innerHTML = this.errors.map(error => 
        `<div class="error-message">${error}</div>`
      ).join('');
      errorContainer.style.display = 'block';
    } else {
      errorContainer.style.display = 'none';
    }
  }
}

// Auto-validate forms on load
document.addEventListener('DOMContentLoaded', function() {
  document.querySelectorAll('.validated-form').forEach(form => {
    const validator = new SlotValidator(form);
    
    // Validate on initialization
    if (!validator.validate()) {
      validator.showErrors();
    }
    
    // Re-validate on content changes
    const observer = new MutationObserver(() => {
      validator.validate();
      validator.showErrors();
    });
    
    observer.observe(form, {
      childList: true,
      subtree: true,
      characterData: true
    });
  });
});
</script>
```

### Performance-Optimized Slot Compilation

Optimize slot rendering for large applications:

**`src/.components/optimized-list.html`**:
```html
<!-- High-Performance List Component -->
<div class="optimized-list" 
     data-virtual-scrolling="false"
     data-lazy-render="false"
     data-chunk-size="50">
  
  <!-- List header -->
  <header class="list-header">
    <slot name="header">
      <h3>List Items</h3>
    </slot>
    
    <!-- List controls -->
    <div class="list-controls">
      <slot name="controls">
        <!-- Filter, sort, pagination controls -->
      </slot>
    </div>
  </header>
  
  <!-- Virtual scrolling container -->
  <div class="list-container" data-virtualized="false">
    <!-- Template for list items -->
    <template class="list-item-template">
      <div class="list-item" data-item-index="">
        <slot name="item">
          <div class="item-content">
            <slot name="item-content">
              <!-- Default item content -->
            </slot>
          </div>
        </slot>
      </div>
    </template>
    
    <!-- Rendered items container -->
    <div class="list-items">
      <slot name="items">
        <!-- List items will be rendered here -->
      </slot>
    </div>
    
    <!-- Loading indicator -->
    <div class="list-loading" style="display: none;">
      <slot name="loading">
        <div class="spinner">Loading...</div>
      </slot>
    </div>
  </div>
  
  <!-- List footer -->
  <footer class="list-footer">
    <slot name="footer">
      <!-- Pagination, load more, etc. -->
    </slot>
  </footer>
</div>

<script>
// High-performance list rendering
class OptimizedList {
  constructor(element) {
    this.element = element;
    this.items = [];
    this.renderedItems = new Map();
    this.chunkSize = parseInt(element.getAttribute('data-chunk-size')) || 50;
    this.virtualScrolling = element.getAttribute('data-virtual-scrolling') === 'true';
    this.lazyRender = element.getAttribute('data-lazy-render') === 'true';
    
    this.init();
  }
  
  init() {
    this.container = this.element.querySelector('.list-items');
    this.template = this.element.querySelector('.list-item-template');
    
    if (this.virtualScrolling) {
      this.setupVirtualScrolling();
    }
    
    if (this.lazyRender) {
      this.setupLazyRendering();
    }
  }
  
  setItems(items) {
    this.items = items;
    this.renderItems();
  }
  
  renderItems() {
    const startTime = performance.now();
    
    // Clear existing items
    this.container.innerHTML = '';
    this.renderedItems.clear();
    
    // Render in chunks to avoid blocking
    this.renderChunk(0, () => {
      const endTime = performance.now();
      console.log(`Rendered ${this.items.length} items in ${endTime - startTime}ms`);
    });
  }
  
  renderChunk(startIndex, callback) {
    const endIndex = Math.min(startIndex + this.chunkSize, this.items.length);
    
    for (let i = startIndex; i < endIndex; i++) {
      this.renderItem(i);
    }
    
    if (endIndex < this.items.length) {
      // Continue with next chunk
      requestAnimationFrame(() => {
        this.renderChunk(endIndex, callback);
      });
    } else if (callback) {
      callback();
    }
  }
  
  renderItem(index) {
    const item = this.items[index];
    const itemElement = this.template.content.cloneNode(true);
    
    // Set item data
    const itemDiv = itemElement.querySelector('.list-item');
    itemDiv.setAttribute('data-item-index', index);
    
    // Populate slots if item has slot data
    if (item.slots) {
      Object.entries(item.slots).forEach(([slotName, content]) => {
        const slot = itemDiv.querySelector(`slot[name="${slotName}"]`);
        if (slot) {
          slot.innerHTML = content;
        }
      });
    }
    
    this.container.appendChild(itemElement);
    this.renderedItems.set(index, itemDiv);
  }
  
  setupVirtualScrolling() {
    // Implement virtual scrolling for large lists
    this.element.setAttribute('data-virtualized', 'true');
    
    // Calculate item height and viewport
    this.itemHeight = 100; // Default, should be measured
    this.viewportHeight = this.container.clientHeight;
    this.visibleCount = Math.ceil(this.viewportHeight / this.itemHeight) + 5; // Buffer
    
    this.container.addEventListener('scroll', this.handleVirtualScroll.bind(this));
  }
  
  handleVirtualScroll() {
    const scrollTop = this.container.scrollTop;
    const startIndex = Math.floor(scrollTop / this.itemHeight);
    const endIndex = Math.min(startIndex + this.visibleCount, this.items.length);
    
    // Only render visible items
    this.renderVisibleRange(startIndex, endIndex);
  }
  
  renderVisibleRange(startIndex, endIndex) {
    // Clear container
    this.container.innerHTML = '';
    
    // Add spacer for items above viewport
    if (startIndex > 0) {
      const spacer = document.createElement('div');
      spacer.style.height = `${startIndex * this.itemHeight}px`;
      this.container.appendChild(spacer);
    }
    
    // Render visible items
    for (let i = startIndex; i < endIndex; i++) {
      this.renderItem(i);
    }
    
    // Add spacer for items below viewport
    const remainingItems = this.items.length - endIndex;
    if (remainingItems > 0) {
      const spacer = document.createElement('div');
      spacer.style.height = `${remainingItems * this.itemHeight}px`;
      this.container.appendChild(spacer);
    }
  }
  
  setupLazyRendering() {
    // Implement intersection observer for lazy loading
    this.observer = new IntersectionObserver((entries) => {
      entries.forEach(entry => {
        if (entry.isIntersecting) {
          const index = parseInt(entry.target.getAttribute('data-item-index'));
          this.loadItemData(index);
        }
      });
    });
  }
  
  loadItemData(index) {
    // Load additional data for item if needed
    const item = this.items[index];
    if (item.lazyLoad && !item.loaded) {
      // Simulate loading additional data
      this.showLoading(index);
      
      setTimeout(() => {
        item.loaded = true;
        this.hideLoading(index);
        this.updateItem(index, item);
      }, 500);
    }
  }
  
  showLoading(index) {
    const itemElement = this.renderedItems.get(index);
    if (itemElement) {
      itemElement.classList.add('loading');
    }
  }
  
  hideLoading(index) {
    const itemElement = this.renderedItems.get(index);
    if (itemElement) {
      itemElement.classList.remove('loading');
    }
  }
  
  updateItem(index, item) {
    const itemElement = this.renderedItems.get(index);
    if (itemElement && item.slots) {
      Object.entries(item.slots).forEach(([slotName, content]) => {
        const slot = itemElement.querySelector(`slot[name="${slotName}"]`);
        if (slot) {
          slot.innerHTML = content;
        }
      });
    }
  }
}

// Initialize optimized lists
document.addEventListener('DOMContentLoaded', function() {
  document.querySelectorAll('.optimized-list').forEach(element => {
    new OptimizedList(element);
  });
});
</script>
```

## Slot Content Management

### Dynamic Slot Content Injection

Manage slot content dynamically at runtime:

```javascript
// Slot Content Manager
class SlotContentManager {
  constructor() {
    this.contentRegistry = new Map();
    this.observers = new Map();
  }
  
  // Register dynamic content for slots
  registerSlotContent(componentSelector, slotName, contentGenerator) {
    const key = `${componentSelector}:${slotName}`;
    this.contentRegistry.set(key, contentGenerator);
    
    // Apply to existing components
    document.querySelectorAll(componentSelector).forEach(component => {
      this.injectSlotContent(component, slotName, contentGenerator);
    });
    
    // Watch for new components
    this.watchForComponents(componentSelector, slotName, contentGenerator);
  }
  
  injectSlotContent(component, slotName, contentGenerator) {
    const slot = component.querySelector(`slot[name="${slotName}"]`);
    if (slot) {
      const content = contentGenerator(component);
      if (content) {
        // Create slot content element
        const slotElement = document.createElement('div');
        slotElement.setAttribute('slot', slotName);
        slotElement.innerHTML = content;
        
        // Insert before the component to fill the slot
        component.parentNode.insertBefore(slotElement, component);
      }
    }
  }
  
  watchForComponents(selector, slotName, contentGenerator) {
    const observer = new MutationObserver((mutations) => {
      mutations.forEach(mutation => {
        mutation.addedNodes.forEach(node => {
          if (node.nodeType === Node.ELEMENT_NODE) {
            if (node.matches(selector)) {
              this.injectSlotContent(node, slotName, contentGenerator);
            }
            
            // Check descendants
            node.querySelectorAll(selector).forEach(component => {
              this.injectSlotContent(component, slotName, contentGenerator);
            });
          }
        });
      });
    });
    
    observer.observe(document.body, {
      childList: true,
      subtree: true
    });
    
    this.observers.set(`${selector}:${slotName}`, observer);
  }
  
  // Update slot content for specific components
  updateSlotContent(componentSelector, slotName, data) {
    document.querySelectorAll(componentSelector).forEach(component => {
      const slotElement = component.parentNode.querySelector(`[slot="${slotName}"]`);
      if (slotElement) {
        const key = `${componentSelector}:${slotName}`;
        const contentGenerator = this.contentRegistry.get(key);
        if (contentGenerator) {
          slotElement.innerHTML = contentGenerator(component, data);
        }
      }
    });
  }
  
  // Remove slot content
  clearSlotContent(componentSelector, slotName) {
    document.querySelectorAll(componentSelector).forEach(component => {
      const slotElement = component.parentNode.querySelector(`[slot="${slotName}"]`);
      if (slotElement) {
        slotElement.remove();
      }
    });
  }
}

// Usage example
const slotManager = new SlotContentManager();

// Register dynamic user info for profile cards
slotManager.registerSlotContent(
  '.profile-card',
  'user-status',
  (component, data) => {
    const userId = component.getAttribute('data-user-id');
    return `<span class="status online">Online</span>`;
  }
);

// Register dynamic timestamps
slotManager.registerSlotContent(
  '.timestamp-card',
  'relative-time',
  (component) => {
    const timestamp = component.getAttribute('data-timestamp');
    const date = new Date(timestamp);
    return `<time title="${date.toLocaleString()}">${timeAgo(date)}</time>`;
  }
);
```

### Slot Event System

Implement event-driven slot updates:

```javascript
// Slot Event System
class SlotEventSystem {
  constructor() {
    this.eventListeners = new Map();
    this.setupGlobalListeners();
  }
  
  setupGlobalListeners() {
    // Listen for custom slot events
    document.addEventListener('slot:update', (event) => {
      this.handleSlotUpdate(event);
    });
    
    document.addEventListener('slot:clear', (event) => {
      this.handleSlotClear(event);
    });
    
    document.addEventListener('slot:refresh', (event) => {
      this.handleSlotRefresh(event);
    });
  }
  
  handleSlotUpdate(event) {
    const { component, slotName, content } = event.detail;
    this.updateSlot(component, slotName, content);
  }
  
  handleSlotClear(event) {
    const { component, slotName } = event.detail;
    this.clearSlot(component, slotName);
  }
  
  handleSlotRefresh(event) {
    const { component, slotName } = event.detail;
    this.refreshSlot(component, slotName);
  }
  
  updateSlot(component, slotName, content) {
    let slotElement = component.parentNode.querySelector(`[slot="${slotName}"]`);
    
    if (!slotElement) {
      slotElement = document.createElement('div');
      slotElement.setAttribute('slot', slotName);
      component.parentNode.insertBefore(slotElement, component);
    }
    
    slotElement.innerHTML = content;
    
    // Trigger updated event
    component.dispatchEvent(new CustomEvent('slot:updated', {
      detail: { slotName, content }
    }));
  }
  
  clearSlot(component, slotName) {
    const slotElement = component.parentNode.querySelector(`[slot="${slotName}"]`);
    if (slotElement) {
      slotElement.remove();
      
      component.dispatchEvent(new CustomEvent('slot:cleared', {
        detail: { slotName }
      }));
    }
  }
  
  refreshSlot(component, slotName) {
    // Re-evaluate slot content based on current component state
    const slot = component.querySelector(`slot[name="${slotName}"]`);
    if (slot) {
      const refreshHandler = slot.getAttribute('data-refresh-handler');
      if (refreshHandler && window[refreshHandler]) {
        const newContent = window[refreshHandler](component);
        this.updateSlot(component, slotName, newContent);
      }
    }
  }
  
  // Utility methods for triggering slot events
  static updateSlot(component, slotName, content) {
    document.dispatchEvent(new CustomEvent('slot:update', {
      detail: { component, slotName, content }
    }));
  }
  
  static clearSlot(component, slotName) {
    document.dispatchEvent(new CustomEvent('slot:clear', {
      detail: { component, slotName }
    }));
  }
  
  static refreshSlot(component, slotName) {
    document.dispatchEvent(new CustomEvent('slot:refresh', {
      detail: { component, slotName }
    }));
  }
}

// Initialize slot event system
const slotEventSystem = new SlotEventSystem();

// Usage examples
function updateUserProfile(userId, profileData) {
  const profileCard = document.querySelector(`[data-user-id="${userId}"]`);
  if (profileCard) {
    SlotEventSystem.updateSlot(
      profileCard,
      'user-info',
      `<h3>${profileData.name}</h3><p>${profileData.title}</p>`
    );
  }
}

function refreshAllTimestamps() {
  document.querySelectorAll('.timestamp-card').forEach(card => {
    SlotEventSystem.refreshSlot(card, 'relative-time');
  });
}
```

## Best Practices for Complex Applications

### Slot Architecture Guidelines

1. **Hierarchical Organization**: Design slot inheritance hierarchies that mirror your content structure
2. **Clear Naming Conventions**: Use descriptive, consistent slot names across components
3. **Documentation**: Document slot interfaces and expected content types
4. **Performance Considerations**: Minimize nested slot resolution for better performance
5. **Error Handling**: Provide meaningful fallbacks for missing or invalid slot content

### Component Composition Strategies

```html
<!-- Example: Complex Dashboard Layout -->

<!-- Main dashboard layout -->
<div data-include=".components/layouts/dashboard.html">
  <!-- Dashboard header -->
  <div slot="header">
    <div data-include=".components/dashboard/header.html">
      <div slot="title">Analytics Dashboard</div>
      <div slot="actions">
        <div data-include=".components/ui/button-group.html">
          <div slot="buttons">
            <div data-include=".components/ui/button.html" data-style="outline">
              <div slot="content">Export</div>
            </div>
            <div data-include=".components/ui/button.html" data-style="primary">
              <div slot="content">Refresh</div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
  
  <!-- Dashboard sidebar -->
  <div slot="sidebar">
    <div data-include=".components/dashboard/sidebar.html">
      <div slot="navigation">
        <div data-include=".components/ui/nav-menu.html">
          <div slot="items">
            <!-- Navigation items -->
          </div>
        </div>
      </div>
    </div>
  </div>
  
  <!-- Dashboard main content -->
  <div slot="main">
    <div data-include=".components/dashboard/grid.html" data-columns="12">
      <div slot="widgets">
        <!-- Dashboard widgets -->
        <div data-include=".components/widgets/metric-card.html" data-grid-column="span 4">
          <div slot="title">Total Users</div>
          <div slot="value">1,234</div>
          <div slot="trend">+5.2%</div>
        </div>
        
        <div data-include=".components/widgets/chart-card.html" data-grid-column="span 8">
          <div slot="title">Traffic Overview</div>
          <div slot="chart-data"><!-- Chart data --></div>
        </div>
      </div>
    </div>
  </div>
</div>
```

### Performance Optimization Techniques

1. **Slot Caching**: Cache resolved slot content to avoid re-computation
2. **Lazy Slot Resolution**: Defer slot resolution until content is actually needed
3. **Virtual Slot Rendering**: Use virtual scrolling for large lists with slot-based items
4. **Batch Slot Updates**: Group multiple slot updates to minimize DOM manipulation

### Testing Slot-Based Components

```javascript
// Slot testing utilities
class SlotTester {
  constructor(componentPath) {
    this.componentPath = componentPath;
    this.testResults = [];
  }
  
  async testSlot(slotName, testContent, expectedBehavior) {
    try {
      // Create test container
      const testContainer = document.createElement('div');
      document.body.appendChild(testContainer);
      
      // Load component
      const response = await fetch(this.componentPath);
      const componentHTML = await response.text();
      
      // Create component with test content
      testContainer.innerHTML = `
        <div data-include="${this.componentPath}">
          <div slot="${slotName}">${testContent}</div>
        </div>
      `;
      
      // Wait for component resolution
      await new Promise(resolve => setTimeout(resolve, 100));
      
      // Test expected behavior
      const result = await expectedBehavior(testContainer);
      
      this.testResults.push({
        slotName,
        passed: result.success,
        message: result.message
      });
      
      // Cleanup
      document.body.removeChild(testContainer);
      
    } catch (error) {
      this.testResults.push({
        slotName,
        passed: false,
        message: error.message
      });
    }
  }
  
  generateReport() {
    const passed = this.testResults.filter(r => r.passed).length;
    const total = this.testResults.length;
    
    console.log(`Slot Tests: ${passed}/${total} passed`);
    
    this.testResults.forEach(result => {
      const status = result.passed ? '✅' : '❌';
      console.log(`${status} ${result.slotName}: ${result.message}`);
    });
  }
}

// Usage example
async function testCardComponent() {
  const tester = new SlotTester('.components/ui/card.html');
  
  await tester.testSlot('title', '<h3>Test Title</h3>', (container) => {
    const title = container.querySelector('h3');
    return {
      success: title && title.textContent === 'Test Title',
      message: title ? 'Title rendered correctly' : 'Title not found'
    };
  });
  
  await tester.testSlot('content', '<p>Test content</p>', (container) => {
    const content = container.querySelector('p');
    return {
      success: content && content.textContent === 'Test content',
      message: content ? 'Content rendered correctly' : 'Content not found'
    };
  });
  
  tester.generateReport();
}
```

## Migration and Refactoring

### Converting to Slot-Based Architecture

Transform existing components to use advanced slot patterns:

**Before (basic includes)**:
```html
<div data-include="header.html"></div>
<div data-include="content.html"></div>
<div data-include="footer.html"></div>
```

**After (slot-based composition)**:
```html
<div data-include=".components/layouts/page.html">
  <div slot="header">
    <div data-include=".components/header.html">
      <div slot="navigation"><!-- Custom nav --></div>
    </div>
  </div>
  
  <div slot="content">
    <div data-include=".components/content-area.html">
      <div slot="main"><!-- Main content --></div>
      <div slot="sidebar"><!-- Sidebar content --></div>
    </div>
  </div>
  
  <div slot="footer">
    <div data-include=".components/footer.html">
      <div slot="links"><!-- Footer links --></div>
    </div>
  </div>
</div>
```

## Next Steps

### Advanced Topics
- [Performance Optimization for Large Applications](../guides/performance-optimization.md)
- [Component Testing Strategies](../guides/component-testing.md)
- [Build-Time Slot Optimization](../guides/build-optimization.md)

### Integration Examples
- [Headless CMS Integration with Slots](../integrations/headless-cms-slots.md)
- [API-Driven Slot Content](../integrations/api-driven-content.md)
- [Real-Time Slot Updates](../integrations/realtime-updates.md)

### Community Resources
- [Advanced Slot Patterns](https://github.com/fwdslsh/unify-examples)
- [Performance Benchmarks](https://github.com/orgs/fwdslsh/discussions)
- [Contributing Slot Improvements](../contributing/contributing.md)

**You're now ready to build sophisticated, scalable applications with advanced slot and template injection patterns!** 🏗️

---

*This guide references the [Unify include-syntax.md](https://github.com/fwdslsh/unify/blob/main/docs/include-syntax.md) and [templating-quick-start.md](https://github.com/fwdslsh/unify/blob/main/docs/templating-quick-start.md) documentation. For the latest advanced patterns, visit [fwdslsh.dev](https://fwdslsh.dev).*