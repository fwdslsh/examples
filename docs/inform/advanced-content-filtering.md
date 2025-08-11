# Advanced Content Filtering with Inform

Master Inform's content extraction capabilities with sophisticated filtering techniques, custom selectors, and extraction patterns for complex websites. This guide covers enterprise-scale content processing and quality control.

## Overview

Inform's advanced filtering features include:
- **Custom CSS selectors** for precise content targeting
- **Content quality filters** for noise reduction
- **Domain-specific extractors** for specialized sites
- **AI-powered content classification** for automatic filtering
- **Rule-based content processing** with configurable patterns
- **Multi-pass extraction** for complex site structures

## Custom Selector Configuration

### Advanced CSS Selectors

Define sophisticated extraction rules:

**`.inform.yaml`**:
```yaml
# Advanced content extraction configuration
extraction:
  # Primary content selectors (ordered by priority)
  content_selectors:
    # Article-based sites
    - selector: "article[role='main']"
      priority: 10
      description: "Main article content with semantic role"
      
    - selector: "main article, [role='main'] article"
      priority: 9
      description: "Articles within main content areas"
      
    - selector: ".post-content, .entry-content, .article-content"
      priority: 8
      description: "Common content class patterns"
      
    # Documentation sites
    - selector: ".documentation-content, .docs-content"
      priority: 10
      context: "documentation"
      
    - selector: ".markdown-body, .rst-content"
      priority: 9
      context: "documentation"
      
    # Blog platforms
    - selector: ".hentry .entry-content"
      priority: 8
      context: "blog"
      
    - selector: "[itemtype*='BlogPosting'] [itemprop='articleBody']"
      priority: 9
      context: "blog"
      
    # News sites
    - selector: "[itemtype*='NewsArticle'] [itemprop='articleBody']"
      priority: 10
      context: "news"
      
    - selector: ".story-body, .article-body"
      priority: 8
      context: "news"

  # Content exclusion patterns (what to remove)
  exclude_selectors:
    # Navigation and UI elements
    - "nav, .navigation, .navbar, .menu"
    - "header, .header, .site-header"
    - "footer, .footer, .site-footer"
    - ".sidebar, .aside, aside"
    
    # Advertisements and promotional content
    - ".ad, .ads, .advertisement, .sponsored"
    - "[id*='ad'], [class*='ad-'], [class*='ads-']"
    - ".promo, .promotion, .banner"
    - ".newsletter-signup, .email-signup"
    
    # Social media and sharing
    - ".social, .social-media, .share-buttons"
    - ".twitter-tweet, .facebook-post"
    - ".comments, .comment-section"
    
    # Technical elements
    - "script, style, noscript"
    - ".sr-only, .screen-reader-text"
    - "[aria-hidden='true']"
    
    # Site-specific noise
    - ".cookie-notice, .gdpr-notice"
    - ".popup, .modal, .overlay"
    - ".breadcrumb, .pagination"

  # Advanced selector patterns
  advanced_patterns:
    # Content within specific containers
    - name: "contained_content"
      pattern: "main article, .main-content article, #content article"
      description: "Articles within main content containers"
      
    # Content with minimum length
    - name: "substantial_content"
      pattern: "p:not(:empty)"
      min_length: 50
      description: "Paragraphs with meaningful content"
      
    # Headers with associated content
    - name: "structured_content"
      pattern: "h1, h2, h3, h4, h5, h6"
      include_following: "p, ul, ol, div, section"
      description: "Headers with their associated content"
      
    # Time-sensitive content
    - name: "timestamped_content"
      pattern: "[datetime], .timestamp, .date"
      extract_datetime: true
      description: "Content with temporal information"

# Content quality filters
quality_filters:
  # Text quality metrics
  text_quality:
    min_length: 100
    max_length: 50000
    min_sentences: 3
    min_words: 20
    
    # Language detection
    detect_language: true
    allowed_languages: ["en", "es", "fr", "de", "it", "pt"]
    
    # Content scoring
    readability_score:
      min_score: 30
      algorithm: "flesch_kincaid"
    
    # Spam detection
    spam_indicators:
      - excessive_caps_ratio: 0.3
      - excessive_punctuation: 5
      - repeated_phrases: 3
      - keyword_stuffing_ratio: 0.1

  # Structure quality metrics  
  structure_quality:
    min_paragraphs: 2
    max_empty_elements: 10
    min_heading_hierarchy: false
    
    # HTML validation
    valid_html: true
    semantic_markup: true
    
    # Image requirements
    images:
      require_alt_text: true
      min_resolution: [100, 100]
      allowed_formats: ["jpg", "jpeg", "png", "webp", "svg"]

  # Content freshness
  freshness:
    max_age_days: 365
    require_date: false
    date_sources:
      - "[datetime]"
      - ".published, .date, .timestamp"
      - "meta[property='article:published_time']"
```

### Domain-Specific Extractors

Create specialized extractors for different types of sites:

**Site-specific configurations**:

```yaml
# GitHub documentation extractor
github_docs:
  domains: ["docs.github.com"]
  content_selectors:
    - ".Layout-main .markdown-body"
    - "#readme .markdown-body"
  exclude_selectors:
    - ".js-header-wrapper"
    - ".js-footer"
    - ".js-survey-banner"
  metadata_extraction:
    title: "h1, .js-page-title"
    category: ".js-breadcrumb a:last-child"
    last_updated: "[datetime]"

# Medium articles extractor  
medium:
  domains: ["medium.com", "*.medium.com"]
  content_selectors:
    - "article section"
    - ".postArticle-content"
  exclude_selectors:
    - ".js-actionMultirecommend"
    - ".js-postMetaLockup"
    - ".js-responsesSection"
  metadata_extraction:
    title: "h1[data-testid='storyTitle']"
    author: "[data-testid='authorName']"
    read_time: "[data-testid='storyReadTime']"
    
# Stack Overflow extractor
stackoverflow:
  domains: ["stackoverflow.com"]
  content_selectors:
    - ".js-post-body"
    - ".answercell .js-post-body"
  exclude_selectors:
    - ".js-vote-count"
    - ".js-post-menu"
    - ".js-comment-list"
  metadata_extraction:
    score: ".js-vote-count"
    author: ".user-details a"
    tags: ".js-gps-track"
    
# Wikipedia extractor
wikipedia:
  domains: ["*.wikipedia.org"]
  content_selectors:
    - "#mw-content-text .mw-parser-output"
  exclude_selectors:
    - ".navbox"
    - ".infobox"
    - ".ambox"
    - ".hatnote"
    - "#toc"
  metadata_extraction:
    title: "#firstHeading"
    categories: "#mw-normal-catlinks a"
    last_modified: "#footer-info-lastmod"
```

## Content Processing Pipeline

### Multi-Pass Extraction

Implement sophisticated content processing:

```yaml
# Multi-pass extraction configuration
processing_pipeline:
  passes:
    # Pass 1: Structure identification
    - name: "structure_analysis"
      type: "analyze"
      operations:
        - identify_content_blocks
        - detect_article_structure
        - map_heading_hierarchy
        - find_metadata_sources
      
    # Pass 2: Content extraction  
    - name: "content_extraction"
      type: "extract"
      operations:
        - extract_main_content
        - extract_metadata
        - extract_images
        - extract_links
      
    # Pass 3: Content cleaning
    - name: "content_cleaning"
      type: "clean"
      operations:
        - remove_empty_elements
        - normalize_whitespace
        - fix_broken_links
        - optimize_images
        
    # Pass 4: Content enhancement
    - name: "content_enhancement"  
      type: "enhance"
      operations:
        - generate_excerpts
        - add_table_of_contents
        - create_reading_time
        - tag_content_types
        
    # Pass 5: Quality validation
    - name: "quality_validation"
      type: "validate"
      operations:
        - check_content_quality
        - validate_markdown
        - verify_links
        - assess_completeness

# Operation definitions
operations:
  identify_content_blocks:
    algorithm: "density_based"
    min_text_density: 0.3
    min_block_size: 100
    
  extract_main_content:
    method: "combined"
    strategies:
      - "selector_based"
      - "readability_analysis"
      - "text_density"
      - "dom_analysis"
    confidence_threshold: 0.8
    
  normalize_whitespace:
    collapse_spaces: true
    normalize_line_breaks: true
    trim_lines: true
    max_consecutive_breaks: 2
    
  generate_excerpts:
    method: "automatic"
    length: 160
    preserve_sentences: true
    fallback_to_first_paragraph: true
```

### Custom Content Classifiers

Implement AI-powered content classification:

```yaml
# Content classification configuration
classification:
  # AI-powered content type detection
  content_types:
    - name: "tutorial"
      indicators:
        - patterns: ["step", "tutorial", "guide", "how to", "walkthrough"]
        - structure: ["ordered_list", "numbered_sections"]
        - code_blocks: min_count: 2
        
    - name: "api_documentation"  
      indicators:
        - patterns: ["api", "endpoint", "method", "parameter"]
        - code_blocks: ["json", "curl", "http"]
        - tables: ["parameters", "responses"]
        
    - name: "reference"
      indicators:
        - patterns: ["reference", "documentation", "specification"]
        - structure: ["definition_lists", "nested_headings"]
        - links: ["external_refs", "cross_references"]
        
    - name: "blog_post"
      indicators:
        - metadata: ["author", "published_date", "tags"]
        - structure: ["intro", "body", "conclusion"]
        - engagement: ["comments", "social_shares"]

  # Quality scoring
  quality_metrics:
    - name: "completeness"
      factors:
        - has_title: 1.0
        - has_content: 1.0
        - has_metadata: 0.5
        - has_images: 0.3
        - has_links: 0.2
        
    - name: "readability"
      factors:
        - flesch_score: 0.4
        - sentence_length: 0.3
        - paragraph_length: 0.3
        
    - name: "technical_depth"
      factors:
        - code_examples: 0.5
        - technical_terms: 0.3
        - complexity_score: 0.2

# Custom filtering rules
filtering_rules:
  # Rule-based content filtering
  rules:
    - name: "marketing_content"
      action: "exclude"
      conditions:
        - text_contains: ["buy now", "limited time", "special offer"]
        - class_contains: ["promo", "marketing", "cta"]
        
    - name: "low_quality_content"
      action: "flag"
      conditions:
        - word_count: max: 50
        - sentence_count: max: 2
        - readability_score: max: 20
        
    - name: "duplicate_content"
      action: "dedupe"
      conditions:
        - similarity_threshold: 0.9
        - comparison_method: "text_fingerprint"
        
    - name: "technical_content"
      action: "enhance"
      conditions:
        - code_blocks: min: 1
        - technical_terms: min: 5
      enhancements:
        - add_syntax_highlighting
        - extract_code_examples
        - generate_technical_summary
```

## Advanced Extraction Techniques

### Content Structure Analysis

Analyze and extract content based on page structure:

```python
# Custom content analyzer
class AdvancedContentAnalyzer:
    def __init__(self, config):
        self.config = config
        self.extractors = {
            'readability': ReadabilityExtractor(),
            'density': TextDensityExtractor(),
            'semantic': SemanticExtractor(),
            'visual': VisualExtractor()
        }
    
    def analyze_page_structure(self, html_content):
        """Analyze page structure to identify content areas"""
        soup = BeautifulSoup(html_content, 'html.parser')
        
        # Density-based analysis
        density_blocks = self.analyze_text_density(soup)
        
        # Readability analysis
        readability_scores = self.analyze_readability(soup)
        
        # Semantic analysis
        semantic_regions = self.analyze_semantic_structure(soup)
        
        # Visual hierarchy analysis
        visual_hierarchy = self.analyze_visual_hierarchy(soup)
        
        return {
            'density_blocks': density_blocks,
            'readability_scores': readability_scores,
            'semantic_regions': semantic_regions,
            'visual_hierarchy': visual_hierarchy
        }
    
    def analyze_text_density(self, soup):
        """Calculate text density for each element"""
        elements = soup.find_all(['div', 'section', 'article', 'main'])
        density_map = {}
        
        for element in elements:
            text_length = len(element.get_text(strip=True))
            html_length = len(str(element))
            
            if html_length > 0:
                density = text_length / html_length
                density_map[element] = {
                    'density': density,
                    'text_length': text_length,
                    'html_length': html_length,
                    'element_type': element.name,
                    'classes': element.get('class', [])
                }
        
        # Sort by density score
        sorted_elements = sorted(
            density_map.items(), 
            key=lambda x: x[1]['density'], 
            reverse=True
        )
        
        return sorted_elements
    
    def analyze_semantic_structure(self, soup):
        """Analyze semantic HTML structure"""
        semantic_elements = soup.find_all([
            'article', 'section', 'main', 'aside', 'nav', 'header', 'footer'
        ])
        
        structure = {}
        for element in semantic_elements:
            structure[element.name] = {
                'count': len(soup.find_all(element.name)),
                'positions': [elem.sourceline for elem in soup.find_all(element.name) if hasattr(elem, 'sourceline')],
                'content_length': len(element.get_text(strip=True)),
                'children': [child.name for child in element.find_all() if child.name]
            }
        
        return structure
    
    def extract_with_confidence(self, html_content, url=None):
        """Extract content with confidence scoring"""
        analysis = self.analyze_page_structure(html_content)
        
        # Get multiple extraction candidates
        candidates = []
        
        for extractor_name, extractor in self.extractors.items():
            try:
                result = extractor.extract(html_content, analysis)
                candidates.append({
                    'extractor': extractor_name,
                    'content': result['content'],
                    'confidence': result['confidence'],
                    'metadata': result.get('metadata', {}),
                    'quality_score': self.calculate_quality_score(result['content'])
                })
            except Exception as e:
                self.logger.warning(f"Extractor {extractor_name} failed: {e}")
        
        # Select best candidate based on confidence and quality
        if candidates:
            best_candidate = max(candidates, key=lambda x: x['confidence'] * x['quality_score'])
            
            if best_candidate['confidence'] > self.config.get('min_confidence', 0.7):
                return {
                    'content': best_candidate['content'],
                    'metadata': best_candidate['metadata'],
                    'confidence': best_candidate['confidence'],
                    'quality_score': best_candidate['quality_score'],
                    'extractor_used': best_candidate['extractor']
                }
        
        return None
    
    def calculate_quality_score(self, content):
        """Calculate content quality score"""
        if not content:
            return 0.0
        
        score = 0.0
        factors = self.config.get('quality_factors', {})
        
        # Text length factor
        text_length = len(content.strip())
        if text_length > factors.get('min_length', 100):
            score += 0.3
        
        # Sentence count factor
        sentences = content.count('.') + content.count('!') + content.count('?')
        if sentences >= factors.get('min_sentences', 3):
            score += 0.2
        
        # Word count factor  
        words = len(content.split())
        if words >= factors.get('min_words', 20):
            score += 0.2
        
        # Readability factor
        readability = self.calculate_readability_score(content)
        if readability >= factors.get('min_readability', 30):
            score += 0.3
        
        return min(score, 1.0)
```

### Custom Pattern Matching

Implement sophisticated pattern matching for content extraction:

```yaml
# Pattern-based extraction rules
patterns:
  # Recipe extraction
  recipe_patterns:
    ingredients:
      - selector: ".recipe-ingredients li, .ingredients li"
      - pattern: "(?i)ingredients?:?\\s*(.+?)(?=instructions?|directions?|method)"
      - structured_data: "[itemtype*='Recipe'] [itemprop='recipeIngredient']"
      
    instructions:
      - selector: ".recipe-instructions li, .directions li, .method li"
      - pattern: "(?i)(?:instructions?|directions?|method):?\\s*(.+?)(?=notes?|tips?|$)"
      - structured_data: "[itemtype*='Recipe'] [itemprop='recipeInstructions']"
      
    metadata:
      prep_time: "[itemprop='prepTime'], .prep-time"
      cook_time: "[itemprop='cookTime'], .cook-time"
      servings: "[itemprop='recipeYield'], .servings"

  # Product documentation
  product_docs:
    features:
      - selector: ".features li, .feature-list li"
      - pattern: "(?i)features?:?\\s*(.+?)(?=benefits?|specs?|$)"
      
    specifications:
      - selector: ".specs table, .specifications table"
      - pattern: "(?i)specifications?:?\\s*(.+?)(?=features?|description?|$)"
      
    requirements:
      - selector: ".requirements li, .system-requirements li"
      - pattern: "(?i)requirements?:?\\s*(.+?)(?=installation?|setup?|$)"

  # API documentation
  api_docs:
    endpoints:
      - selector: ".endpoint, .api-endpoint"
      - pattern: "(?i)(GET|POST|PUT|DELETE|PATCH)\\s+(/[^\\s]+)"
      
    parameters:
      - selector: ".parameters table, .params table"
      - extract_table: true
      - headers: ["name", "type", "required", "description"]
      
    examples:
      - selector: ".example code, .code-example"
      - language_detection: true
      - preserve_formatting: true

# Advanced text processing
text_processing:
  # Content normalization
  normalization:
    unicode_normalization: "NFKD"
    encoding: "utf-8"
    line_endings: "unix"
    
    # Text cleaning
    remove_extra_whitespace: true
    fix_smart_quotes: true
    normalize_dashes: true
    remove_zero_width_chars: true
    
  # Language processing
  language:
    detect_language: true
    transliterate_non_latin: false
    preserve_original_language: true
    
    # Text segmentation
    sentence_segmentation: true
    paragraph_detection: true
    heading_extraction: true
    
  # Content enhancement
  enhancement:
    auto_link_detection: true
    markdown_conversion: true
    table_formatting: true
    code_block_detection: true
    
    # Metadata extraction
    extract_dates: true
    extract_authors: true
    extract_topics: true
    extract_keywords: true
```

## Quality Control and Validation

### Content Validation Pipeline

Implement comprehensive content validation:

```yaml
# Content validation configuration
validation:
  # Pre-extraction validation
  pre_extraction:
    - check_page_accessibility
    - validate_html_structure
    - detect_javascript_requirements
    - check_rate_limiting
    
  # Post-extraction validation  
  post_extraction:
    - validate_content_quality
    - check_content_completeness
    - verify_extracted_links
    - validate_image_references
    
  # Content integrity checks
  integrity:
    - duplicate_detection
    - content_fingerprinting
    - change_detection
    - version_tracking

# Validation rules
validation_rules:
  content_quality:
    min_word_count: 50
    max_word_count: 10000
    min_sentence_count: 3
    readability_score: min: 20
    
  structure_quality:
    require_title: true
    require_content: true
    max_empty_paragraphs: 3
    min_heading_levels: 1
    
  metadata_quality:
    require_url: true
    require_timestamp: false
    validate_author: false
    check_canonical_url: true

# Error handling and recovery
error_handling:
  # Extraction failure recovery
  fallback_strategies:
    - use_alternative_selectors
    - try_different_extraction_method
    - reduce_quality_thresholds
    - extract_partial_content
    
  # Content quality issues
  quality_issues:
    low_quality_content:
      action: "flag"
      threshold: 0.3
      
    duplicate_content:
      action: "skip"
      similarity_threshold: 0.9
      
    incomplete_extraction:
      action: "retry"
      max_retries: 2
      
    malformed_content:
      action: "clean"
      cleanup_strategy: "aggressive"
```

## Performance Optimization

### Intelligent Caching

Optimize extraction performance with smart caching:

```yaml
# Performance optimization
performance:
  # Extraction caching
  caching:
    enabled: true
    strategy: "multi_level"
    
    # Memory cache
    memory:
      max_size: "500MB"
      ttl: 3600  # 1 hour
      
    # Disk cache
    disk:
      max_size: "5GB"  
      ttl: 86400  # 24 hours
      compression: "gzip"
      
    # Distributed cache (Redis)
    distributed:
      enabled: false
      host: "localhost"
      port: 6379
      ttl: 604800  # 1 week
      
  # Processing optimization
  processing:
    # Parallel extraction
    parallel_processing: true
    max_workers: 8
    chunk_size: 100
    
    # Memory management
    max_memory_usage: "2GB"
    gc_threshold: 0.8
    
    # Timeout settings
    page_timeout: 30
    extraction_timeout: 60
    total_timeout: 120

# Monitoring and metrics
monitoring:
  metrics:
    extraction_success_rate: true
    processing_time: true
    content_quality_score: true
    cache_hit_ratio: true
    memory_usage: true
    
  alerts:
    - metric: "extraction_success_rate"
      threshold: 0.8
      action: "notify"
      
    - metric: "processing_time"
      threshold: 60
      action: "log"
      
    - metric: "memory_usage"
      threshold: 0.9
      action: "gc"
```

## Advanced Use Cases

### E-commerce Content Extraction

Specialized extraction for product pages:

```yaml
# E-commerce extraction configuration
ecommerce:
  product_extractors:
    # Product information
    basic_info:
      title: "h1, .product-title, [itemprop='name']"
      price: ".price, [itemprop='price']"
      description: ".product-description, [itemprop='description']"
      sku: "[itemprop='sku'], .sku"
      brand: "[itemprop='brand'], .brand"
      
    # Product details  
    specifications:
      selector: ".specs, .specifications, .product-details"
      table_extraction: true
      key_value_pairs: true
      
    # Images and media
    images:
      main_image: ".product-image img, .main-image img"
      gallery: ".product-gallery img, .image-gallery img"
      alt_text_required: true
      
    # Reviews and ratings
    reviews:
      rating: "[itemprop='ratingValue'], .rating"
      review_count: "[itemprop='reviewCount'], .review-count"
      reviews: ".review, [itemprop='review']"
      
    # Availability and shipping
    availability:
      in_stock: "[itemprop='availability'], .availability"
      shipping: ".shipping-info, .delivery-info"
      returns: ".return-policy, .returns"

# Content categorization
categorization:
  auto_categorize: true
  categories:
    - name: "product_page"
      indicators:
        - structured_data: "Product"
        - selectors: [".add-to-cart", ".buy-now", ".price"]
        
    - name: "blog_article"
      indicators:
        - structured_data: "BlogPosting"
        - selectors: [".author", ".published", ".article-content"]
        
    - name: "documentation"
      indicators:
        - url_patterns: ["/docs/", "/documentation/", "/help/"]
        - selectors: [".toc", ".doc-content", ".code-example"]
```

## Troubleshooting and Debugging

### Extraction Debugging Tools

Debug and optimize extraction performance:

```bash
# Debug extraction process
inform debug --url https://example.com --verbose

# Test custom selectors
inform test-selectors --config .inform.yaml --url https://example.com

# Analyze page structure
inform analyze --url https://example.com --output analysis.json

# Validate extraction rules
inform validate-config --config .inform.yaml

# Performance profiling
inform profile --url https://example.com --runs 10

# Quality assessment
inform assess-quality --input extracted/ --output quality-report.json
```

### Common Issues and Solutions

```yaml
# Troubleshooting guide
troubleshooting:
  common_issues:
    - issue: "Empty content extraction"
      causes:
        - "JavaScript-rendered content"
        - "Incorrect selectors"
        - "Content blocked by authentication"
      solutions:
        - "Use headless browser mode"
        - "Update content selectors"
        - "Configure authentication"
        
    - issue: "Poor content quality"
      causes:
        - "Aggressive content filtering"
        - "Wrong extraction method"
        - "Site-specific formatting"
      solutions:
        - "Adjust quality thresholds"
        - "Use alternative extractors"
        - "Create site-specific rules"
        
    - issue: "Slow extraction performance"
      causes:
        - "Large page sizes"
        - "Complex selectors"
        - "Network latency"
      solutions:
        - "Enable content caching"
        - "Optimize selectors"
        - "Use parallel processing"
```

## Next Steps

### Advanced Integration
- [Documentation Migration with Inform](../inform/documentation-migration.md)
- [Inform + AI Content Enhancement](../integrations/inform-ai-enhancement.md)
- [Large-Scale Content Processing](../inform/large-scale-processing.md)

### Specialized Extractors
- [API Documentation Extraction](../inform/api-docs-extraction.md)
- [E-commerce Content Crawling](../inform/ecommerce-extraction.md)
- [Academic Paper Processing](../inform/academic-content.md)

### Quality and Validation
- [Content Quality Assurance](../inform/quality-assurance.md)
- [Automated Testing for Extractors](../inform/extraction-testing.md)
- [Content Validation Workflows](../inform/validation-workflows.md)

**Your Inform setup now handles the most complex content extraction scenarios with enterprise-grade filtering and quality control!** 🎯

---

*This guide references the [Inform content extraction documentation](https://github.com/fwdslsh/inform) and [user-guide.md pattern matching](https://github.com/fwdslsh/inform/blob/main/docs/user-guide.md). For the latest extraction techniques and patterns, visit [fwdslsh.dev](https://fwdslsh.dev).*