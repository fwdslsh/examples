# Combining Inform and Giv for Enhanced Content Extraction and Commit Summaries

## Introduction

The combination of Inform's web crawling capabilities and Giv's AI-powered Git workflow enhancement creates a powerful content pipeline for automated documentation and knowledge management. This integration enables teams to extract high-quality content from various web sources and leverage AI to create intelligent summaries, structured documentation, and enhanced commit messages that track content evolution.

### Content Extraction and AI-Enhanced Documentation Workflow

Modern documentation teams often need to:
- Aggregate content from multiple web sources
- Transform raw HTML into structured documentation  
- Track changes and updates to external content sources
- Generate intelligent summaries and insights from crawled data
- Maintain quality control over automatically extracted content

The Inform + Giv integration addresses these challenges by providing:

**Intelligent Content Extraction**: Inform crawls and extracts content with sophisticated filtering
**AI-Enhanced Processing**: Giv processes extracted content to generate summaries and insights
**Automated Documentation**: Structured output suitable for static site generators and knowledge bases
**Change Tracking**: Git integration with AI-generated commit messages for content updates
**Quality Control**: Validation and filtering mechanisms to ensure content quality

### Use Cases for Combining Web Crawling with AI Summarization

**Documentation Aggregation**: Collect and summarize documentation from multiple vendor websites
**Competitive Analysis**: Extract and analyze competitor product information and documentation
**Knowledge Base Creation**: Build comprehensive knowledge bases from scattered web resources
**Content Migration**: Extract content from legacy systems and transform it for modern platforms
**Research Automation**: Gather and summarize research materials from academic and industry sources

### Tool Integration Benefits

- **Reduced Manual Work**: Automate the tedious process of content extraction and summarization
- **Consistent Quality**: AI-powered processing ensures consistent formatting and structure
- **Change Detection**: Track when external sources update their content
- **Intelligent Insights**: Generate summaries and key insights from large volumes of content
- **Seamless Integration**: Output formats compatible with existing documentation workflows

## Content Extraction Setup

### Configuring Inform for Documentation Sources

Start by setting up Inform to target specific documentation sources:

```bash
# Install Inform
npm install -g @fwdslsh/inform

# Create project structure
mkdir content-extraction-project
cd content-extraction-project
mkdir -p config extracted processed

# Create Inform configuration
cat > config/inform.config.js << 'INNEREOF'
export default {
  // Target documentation sources
  sources: [
    {
      name: 'vendor-docs',
      baseUrl: 'https://docs.vendor.com',
      crawlPaths: [
        '/api/',
        '/guides/',
        '/tutorials/',
        '/reference/'
      ],
      maxDepth: 3,
      respectRobots: true,
      rateLimit: 1000 // 1 second between requests
    },
    {
      name: 'community-wiki',
      baseUrl: 'https://wiki.example.com',
      crawlPaths: ['/technical-docs/'],
      maxDepth: 2,
      customSelectors: {
        title: 'h1.page-title',
        content: '.wiki-content',
        lastModified: '.last-modified'
      }
    }
  ],
  
  // Output configuration
  output: {
    directory: 'extracted/',
    format: 'markdown',
    preserveStructure: true,
    generateIndex: true
  },
  
  // Content filtering
  filters: {
    minLength: 100,
    excludePatterns: [
      'navigation',
      'sidebar',
      'footer',
      'advertisement'
    ],
    includeMetadata: true
  }
}
INNEREOF
```

### Setting Up Crawling Schedules and Targets

Configure automated crawling schedules:

```bash
# Create scheduling configuration
cat > config/schedule.config.js << 'INNEREOF'
export default {
  schedules: [
    {
      name: 'daily-docs-update',
      sources: ['vendor-docs'],
      cron: '0 2 * * *', // Daily at 2 AM
      priority: 'high'
    },
    {
      name: 'weekly-community-sync',
      sources: ['community-wiki'],
      cron: '0 3 * * 0', // Weekly on Sunday at 3 AM
      priority: 'medium'
    }
  ],
  
  notifications: {
    onSuccess: true,
    onFailure: true,
    channels: ['slack', 'email']
  }
}
INNEREOF
```

### Content Filtering and Quality Control

Implement sophisticated content filtering and validation mechanisms to ensure high-quality extracted content:

```bash
# Create advanced quality control script
cat > scripts/quality-control.js << 'INNEREOF'
const fs = require('fs');
const path = require('path');

class ContentQualityController {
    constructor(config = {}) {
        this.minWordCount = config.minWordCount || 50;
        this.maxWordCount = config.maxWordCount || 10000;
        this.requiredElements = config.requiredElements || ['title', 'content'];
        this.excludePatterns = config.excludePatterns || [];
        this.qualityThreshold = config.qualityThreshold || 0.7;
    }

    async validateContent(content, metadata = {}) {
        const checks = [
            this.checkWordCount(content),
            this.checkStructure(content),
            this.checkRelevance(content),
            this.checkDuplication(content),
            this.checkLanguage(content)
        ];

        const results = await Promise.all(checks);
        const overallScore = results.reduce((acc, score) => acc + score, 0) / results.length;

        return {
            passed: overallScore >= this.qualityThreshold,
            score: overallScore,
            details: {
                wordCount: results[0],
                structure: results[1],
                relevance: results[2],
                duplication: results[3],
                language: results[4]
            }
        };
    }

    checkWordCount(content) {
        const words = content.split(/\s+/).filter(word => word.length > 0);
        const count = words.length;
        
        if (count < this.minWordCount) return 0.2;
        if (count > this.maxWordCount) return 0.5;
        if (count >= 100 && count <= 3000) return 1.0;
        return 0.8;
    }

    checkStructure(content) {
        let score = 0.5; // Base score

        // Check for headings
        const headingCount = (content.match(/^#{1,6}\s/gm) || []).length;
        if (headingCount > 0) score += 0.2;
        if (headingCount > 2) score += 0.1;

        // Check for lists
        if (content.includes('- ') || content.includes('1. ')) score += 0.1;

        // Check for code blocks
        if (content.includes('```')) score += 0.1;

        return Math.min(score, 1.0);
    }

    checkRelevance(content) {
        const technicalKeywords = [
            'documentation', 'guide', 'tutorial', 'API', 'example',
            'configuration', 'installation', 'setup', 'usage',
            'troubleshooting', 'reference', 'specification'
        ];

        const contentLower = content.toLowerCase();
        const foundKeywords = technicalKeywords.filter(keyword => 
            contentLower.includes(keyword.toLowerCase())
        );

        return Math.min(foundKeywords.length / 5, 1.0);
    }

    async checkDuplication(content) {
        // Simplified duplication check
        // In production, this would check against a content database
        const contentHash = require('crypto')
            .createHash('sha256')
            .update(content.replace(/\s+/g, ' ').trim())
            .digest('hex');

        // For now, assume no duplication
        return 1.0;
    }

    checkLanguage(content) {
        // Simple English language detection
        const englishWords = [
            'the', 'and', 'or', 'but', 'in', 'on', 'at', 'to', 'for', 'of',
            'with', 'by', 'from', 'up', 'about', 'into', 'through', 'during'
        ];

        const words = content.toLowerCase().split(/\s+/);
        const englishWordCount = words.filter(word => 
            englishWords.includes(word)
        ).length;

        const ratio = englishWordCount / words.length;
        return Math.min(ratio * 10, 1.0); // Scale to 0-1
    }
}

async function filterExtractedContent() {
    console.log('🔍 Applying quality control filters...');
    
    const controller = new ContentQualityController();
    const extractedDir = 'extracted/';
    const filteredDir = 'filtered/';
    
    if (!fs.existsSync(filteredDir)) {
        fs.mkdirSync(filteredDir, { recursive: true });
    }

    const files = getAllMarkdownFiles(extractedDir);
    let passed = 0;
    let total = 0;

    for (const file of files) {
        try {
            total++;
            const content = fs.readFileSync(file, 'utf8');
            const metadata = extractMetadata(content);
            
            const validation = await controller.validateContent(content, metadata);
            
            if (validation.passed) {
                passed++;
                
                // Copy to filtered directory with quality metadata
                const relativePath = path.relative(extractedDir, file);
                const outputPath = path.join(filteredDir, relativePath);
                const outputDir = path.dirname(outputPath);
                
                if (!fs.existsSync(outputDir)) {
                    fs.mkdirSync(outputDir, { recursive: true });
                }
                
                const enhancedContent = addQualityMetadata(content, validation);
                fs.writeFileSync(outputPath, enhancedContent);
                
                console.log(`✅ Passed: ${relativePath} (score: ${validation.score.toFixed(2)})`);
            } else {
                console.log(`❌ Failed: ${path.basename(file)} (score: ${validation.score.toFixed(2)})`);
            }
        } catch (error) {
            console.error(`Error processing ${file}:`, error.message);
        }
    }

    console.log(`🏁 Quality control complete: ${passed}/${total} files passed`);
}

function getAllMarkdownFiles(dir) {
    const files = [];
    
    function scan(currentDir) {
        if (!fs.existsSync(currentDir)) return;
        
        const items = fs.readdirSync(currentDir, { withFileTypes: true });
        
        for (const item of items) {
            const fullPath = path.join(currentDir, item.name);
            
            if (item.isDirectory()) {
                scan(fullPath);
            } else if (item.name.endsWith('.md')) {
                files.push(fullPath);
            }
        }
    }
    
    scan(dir);
    return files;
}

function extractMetadata(content) {
    const metadata = {};
    
    if (content.startsWith('---\n')) {
        const frontmatterEnd = content.indexOf('\n---\n', 4);
        if (frontmatterEnd !== -1) {
            const frontmatter = content.slice(4, frontmatterEnd);
            frontmatter.split('\n').forEach(line => {
                const [key, ...valueParts] = line.split(':');
                if (key && valueParts.length > 0) {
                    metadata[key.trim()] = valueParts.join(':').trim().replace(/^["']|["']$/g, '');
                }
            });
        }
    }
    
    return metadata;
}

function addQualityMetadata(content, validation) {
    const qualityData = `---
qualityChecked: true
qualityScore: ${validation.score.toFixed(2)}
qualityDetails:
  wordCount: ${validation.details.wordCount.toFixed(2)}
  structure: ${validation.details.structure.toFixed(2)}
  relevance: ${validation.details.relevance.toFixed(2)}
  duplication: ${validation.details.duplication.toFixed(2)}
  language: ${validation.details.language.toFixed(2)}
filteredDate: ${new Date().toISOString()}
---

`;

    if (content.startsWith('---\n')) {
        const frontmatterEnd = content.indexOf('\n---\n', 4);
        if (frontmatterEnd !== -1) {
            const existingFrontmatter = content.slice(4, frontmatterEnd);
            const body = content.slice(frontmatterEnd + 5);
            
            return `---
${existingFrontmatter}
qualityChecked: true
qualityScore: ${validation.score.toFixed(2)}
filteredDate: ${new Date().toISOString()}
---${body}`;
        }
    }
    
    return qualityData + content;
}

filterExtractedContent().catch(console.error);
INNEREOF
```

## AI-Enhanced Processing

### Using Giv to Create Summaries of Extracted Content

Integrate Giv for intelligent content processing and summarization:

```bash
# Install and configure Giv
npm install -g @fwdslsh/giv

# Create Giv configuration for content processing
mkdir -p .giv/prompts

cat > .giv/config.json << 'INNEREOF'
{
  "defaultPrompt": "content-summary",
  "prompts": {
    "content-summary": ".giv/prompts/content-summary.md",
    "commit-content": ".giv/prompts/commit-content.md",
    "structured-docs": ".giv/prompts/structured-docs.md"
  },
  "provider": "openai",
  "model": "gpt-4",
  "maxTokens": 1500,
  "temperature": 0.3
}
INNEREOF

# Create comprehensive content summarization prompt
cat > .giv/prompts/content-summary.md << 'INNEREOF'
You are a technical content analyst helping to create intelligent summaries of extracted web content for documentation teams.

## Context
The content has been extracted from various web sources including:
- Technical documentation sites
- API references and specifications
- Tutorials and how-to guides
- Community wikis and forums
- Knowledge bases and support documentation

## Task
Analyze the provided content and create a comprehensive, actionable summary that includes:

1. **Main Topic**: What is the primary subject matter?
2. **Content Type**: Documentation, tutorial, API reference, troubleshooting, etc.
3. **Target Audience**: Developer level and intended users
4. **Key Information**: 3-5 most important concepts or facts
5. **Technical Details**: APIs, commands, configurations mentioned
6. **Action Items**: Specific steps, commands, or implementations described
7. **Prerequisites**: Required knowledge or setup mentioned
8. **Related Topics**: Connected concepts that users should know about

## Output Format
```markdown
## Content Summary

**Topic**: [Main topic]
**Type**: [Content type - Documentation/Tutorial/API Reference/etc.]
**Audience**: [Target audience - Beginner/Intermediate/Advanced Developers]
**Quality**: [High/Medium/Low based on completeness and clarity]

### Key Information
- [Most important point 1]
- [Most important point 2]  
- [Most important point 3]

### Technical Details
- [API endpoint/command/configuration 1]
- [API endpoint/command/configuration 2]

### Prerequisites
- [Required knowledge/tool 1]
- [Required knowledge/tool 2]

### Action Items
1. [Step 1 if applicable]
2. [Step 2 if applicable]
3. [Step 3 if applicable]

### Related Topics
- [Related concept 1]
- [Related concept 2]

### Source Quality Notes
[Brief assessment of content quality, completeness, and potential issues]
```

## Guidelines
- Focus on technical accuracy and actionable information
- Identify any gaps, outdated information, or inconsistencies
- Highlight unique or particularly valuable insights
- Note if content appears to be incomplete or requires updates
- Emphasize practical, implementable information
INNEREOF

# Create structured documentation prompt
cat > .giv/prompts/structured-docs.md << 'INNEREOF'
You are a technical writer helping to transform extracted web content into well-structured, professional documentation.

## Task
Transform the provided extracted content into properly structured technical documentation that follows documentation best practices and is optimized for developer use.

## Structure Requirements
1. **Clear Information Hierarchy**: Logical flow from overview to details
2. **Consistent Markdown Formatting**: Proper use of headers, lists, code blocks
3. **Code Examples**: Well-formatted code with syntax highlighting
4. **Cross-References**: Internal links and references where appropriate
5. **Professional Tone**: Clear, concise, and technically accurate language

## Output Format
```markdown
---
title: [Clear, descriptive title]
description: [Concise description of what this covers]
category: [API/Tutorial/Guide/Reference/etc.]
difficulty: [beginner|intermediate|advanced]
tags: [relevant, technical, tags]
lastUpdated: [current date]
---

# [Document Title]

[Brief introductory paragraph explaining what this document covers and who it's for]

## Overview

[High-level summary of the topic, its purpose, and key benefits]

## Prerequisites

[List any required knowledge, tools, or setup needed]

## [Main Content Section 1]

[Well-organized content with clear explanations]

### [Subsection if needed]

[Detailed information with examples]

```bash
# Code examples with proper syntax highlighting
command --option value
```

## [Additional Sections as Appropriate]

[Continue organizing content logically]

### Code Examples

```javascript
// Practical, working code examples
const example = {
    key: 'value'
};
```

### Configuration

```yaml
# Configuration file examples
setting: value
nested:
  option: true
```

## Common Issues and Troubleshooting

[Address potential problems and solutions]

## Related Resources

- [Link to related documentation]
- [Link to API reference]
- [Link to additional tutorials]

## Next Steps

[Suggested next actions or learning path]
```

## Guidelines
- Maintain all technical accuracy from the original content
- Improve clarity and readability without changing meaning
- Add helpful structure where the original content lacks organization
- Ensure all code examples are properly formatted
- Include helpful context and explanations where beneficial
- Remove or consolidate redundant information
- Add appropriate cross-references and related links
INNEREOF
```

### Generating Commit Messages for Content Updates

Create sophisticated Git workflow integration with AI-powered commit messages:

```bash
# Create commit message prompt for content operations
cat > .giv/prompts/commit-content.md << 'INNEREOF'
You are helping to create informative, standardized commit messages for automated content extraction and processing workflows.

## Context
This repository manages a content pipeline that:
- Extracts content from external web sources using Inform
- Processes content through quality filters and validation
- Enhances content with AI-powered summarization and structuring
- Maintains documentation through automated Git workflows

## Commit Types
- **extract**: New content extracted from web sources
- **update**: Existing content updated from sources
- **process**: Content processed with AI enhancement
- **filter**: Content filtered through quality control
- **aggregate**: Multiple sources combined or consolidated
- **fix**: Corrections to extraction or processing issues

## Instructions
Analyze the git diff and create a commit message that clearly communicates:

1. **Operation Type**: What kind of content operation was performed
2. **Content Scope**: What type of content or which sources
3. **Scale**: Number of files or volume of changes when significant
4. **Quality Info**: Notable quality improvements or issues addressed

## Format
```
[type]: [action] [content description] [scope/source]

Optional detailed description:
- Specific details about extraction sources or processing
- Number of files affected when significant (>5 files)
- Quality metrics or improvements
- Any notable issues resolved or sources added
```

## Examples
- `extract: Add API documentation from vendor portal (23 files)`
- `update: Refresh tutorial content from community wiki`
- `process: Enhance 15 guides with AI summarization and restructuring`
- `filter: Apply quality control to documentation sources`
- `aggregate: Combine duplicate content from multiple vendor sources`
- `fix: Resolve extraction issues with updated selectors`

## Guidelines
- Be specific about the content type (API docs, tutorials, guides, etc.)
- Include source information when relevant (vendor name, wiki, etc.)
- Mention file counts for significant changes (>5 files)
- Focus on the value added by the operation
- Keep the first line under 72 characters
- Use present tense and imperative mood
INNEREOF

# Create automated content processing script
cat > scripts/process-with-giv.js << 'INNEREOF'
const fs = require('fs');
const path = require('path');
const { execSync } = require('child_process');

class ContentProcessor {
    constructor() {
        this.filteredDir = 'filtered/';
        this.processedDir = 'processed/';
        this.tempDir = '/tmp/content-processing/';
        
        // Ensure directories exist
        [this.processedDir, this.tempDir].forEach(dir => {
            if (!fs.existsSync(dir)) {
                fs.mkdirSync(dir, { recursive: true });
            }
        });
    }

    async processAllContent() {
        console.log('🤖 Starting AI-enhanced content processing...');
        
        const files = this.getAllMarkdownFiles(this.filteredDir);
        const processingStats = {
            total: files.length,
            processed: 0,
            summaries: 0,
            structured: 0,
            errors: 0
        };

        for (const file of files) {
            try {
                console.log(`🔄 Processing ${path.basename(file)}...`);
                
                const result = await this.processFile(file);
                
                if (result.success) {
                    processingStats.processed++;
                    if (result.summary) processingStats.summaries++;
                    if (result.structured) processingStats.structured++;
                    
                    console.log(`✅ Enhanced: ${path.basename(file)} (${result.processingTime}ms)`);
                } else {
                    processingStats.errors++;
                    console.log(`⚠️ Partial: ${path.basename(file)} - ${result.error}`);
                }
                
            } catch (error) {
                processingStats.errors++;
                console.error(`❌ Failed: ${path.basename(file)} - ${error.message}`);
            }
        }

        // Generate processing report
        await this.generateProcessingReport(processingStats);
        
        // Generate content index
        await this.generateContentIndex();
        
        console.log(`🏁 Content processing complete: ${processingStats.processed}/${processingStats.total} files enhanced`);
    }

    async processFile(filePath) {
        const startTime = Date.now();
        const content = fs.readFileSync(filePath, 'utf8');
        
        try {
            // Generate AI summary
            const summary = await this.generateAISummary(content, filePath);
            
            // Create structured documentation
            const structured = await this.createStructuredDoc(content, filePath);
            
            // Combine and save enhanced content
            const enhancedContent = this.combineEnhancements(content, summary, structured);
            
            // Save to processed directory
            const relativePath = path.relative(this.filteredDir, filePath);
            const outputPath = path.join(this.processedDir, relativePath);
            const outputDir = path.dirname(outputPath);
            
            if (!fs.existsSync(outputDir)) {
                fs.mkdirSync(outputDir, { recursive: true });
            }
            
            fs.writeFileSync(outputPath, enhancedContent);
            
            // Save individual summary file
            const summaryPath = outputPath.replace('.md', '.summary.md');
            fs.writeFileSync(summaryPath, summary);
            
            return {
                success: true,
                summary: !!summary,
                structured: !!structured,
                processingTime: Date.now() - startTime
            };
            
        } catch (error) {
            return {
                success: false,
                error: error.message,
                processingTime: Date.now() - startTime
            };
        }
    }

    async generateAISummary(content, filePath) {
        try {
            // Create temporary file for Giv processing
            const tempFile = path.join(this.tempDir, `summary-${Date.now()}.md`);
            fs.writeFileSync(tempFile, content);
            
            // Use Giv to generate intelligent summary
            const summary = execSync(
                `giv analyze --file "${tempFile}" --prompt content-summary --format markdown`,
                { encoding: 'utf8', timeout: 30000 }
            );
            
            // Clean up temp file
            fs.unlinkSync(tempFile);
            
            return summary.trim();
            
        } catch (error) {
            console.error(`AI summary generation failed for ${path.basename(filePath)}:`, error.message);
            return this.generateFallbackSummary(content);
        }
    }

    async createStructuredDoc(content, filePath) {
        try {
            // Create temporary file for Giv processing  
            const tempFile = path.join(this.tempDir, `structure-${Date.now()}.md`);
            fs.writeFileSync(tempFile, content);
            
            // Use Giv to create structured documentation
            const structured = execSync(
                `giv restructure --file "${tempFile}" --prompt structured-docs --format markdown`,
                { encoding: 'utf8', timeout: 45000 }
            );
            
            // Clean up temp file
            fs.unlinkSync(tempFile);
            
            return structured.trim();
            
        } catch (error) {
            console.error(`Document structuring failed for ${path.basename(filePath)}:`, error.message);
            return content; // Return original content as fallback
        }
    }

    generateFallbackSummary(content) {
        const lines = content.split('\n').filter(line => line.trim());
        const title = lines.find(line => line.startsWith('# ')) || 'Extracted Content';
        const wordCount = content.split(/\s+/).length;
        const hasCode = content.includes('```');
        const hasLinks = content.includes('http') || content.includes('[');
        
        return `## Content Summary

**Topic**: ${title.replace('# ', '')}
**Type**: Documentation
**Word Count**: ${wordCount}
**Has Code Examples**: ${hasCode ? 'Yes' : 'No'}
**Has External Links**: ${hasLinks ? 'Yes' : 'No'}

### Key Information
- Content extracted from external web source
- Automated processing applied for quality enhancement
- Manual review recommended for accuracy verification

### Technical Details
${hasCode ? '- Contains code examples or configuration snippets' : '- Primarily text-based documentation'}

### Source Quality Notes
Fallback summary generated due to AI processing limitations. Content may benefit from manual review and enhancement.`;
    }

    combineEnhancements(originalContent, summary, structured) {
        const timestamp = new Date().toISOString();
        const processingMetadata = `---
aiProcessed: true
processedDate: ${timestamp}
hasAiSummary: ${!!summary}
hasStructuredVersion: ${!!structured}
processingVersion: "1.0"
---

`;

        return processingMetadata + `# AI-Enhanced Content Documentation

*This document has been automatically processed and enhanced with AI analysis.*

## AI-Generated Summary

${summary}

---

## Structured Documentation

${structured}

---

## Original Extracted Content

${originalContent}

---

*Processing completed on ${new Date().toLocaleString()}*`;
    }

    async generateProcessingReport(stats) {
        const report = `# Content Processing Report

**Processing Date**: ${new Date().toISOString()}
**Total Files**: ${stats.total}

## Processing Results

- **Successfully Processed**: ${stats.processed} files (${((stats.processed/stats.total)*100).toFixed(1)}%)
- **AI Summaries Generated**: ${stats.summaries} files
- **Structured Documentation**: ${stats.structured} files
- **Processing Errors**: ${stats.errors} files

## Quality Metrics

- **Success Rate**: ${((stats.processed/stats.total)*100).toFixed(1)}%
- **AI Enhancement Rate**: ${((stats.summaries/stats.total)*100).toFixed(1)}%
- **Structure Enhancement Rate**: ${((stats.structured/stats.total)*100).toFixed(1)}%

## Next Steps

${stats.errors > 0 ? 
  `⚠️ Review ${stats.errors} files that encountered processing errors` :
  '✅ All files processed successfully'
}

${stats.processed < stats.total ? 
  '🔄 Consider re-running processing for failed files' :
  '🎉 All content successfully enhanced with AI processing'
}

---
*Report generated automatically by content processing system*
`;

        const reportsDir = 'reports/';
        if (!fs.existsSync(reportsDir)) {
            fs.mkdirSync(reportsDir, { recursive: true });
        }
        
        const reportPath = path.join(reportsDir, `processing-report-${Date.now()}.md`);
        fs.writeFileSync(reportPath, report);
        
        console.log(`📊 Processing report saved: ${reportPath}`);
    }

    async generateContentIndex() {
        console.log('📋 Generating enhanced content index...');
        
        const files = this.getAllMarkdownFiles(this.processedDir);
        
        let indexContent = `---
title: "AI-Enhanced Content Index"
description: "Complete index of extracted and AI-processed content"
generated: ${new Date().toISOString()}
totalFiles: ${files.length}
processingVersion: "1.0"
---

# AI-Enhanced Content Index

*Generated on ${new Date().toLocaleString()}*

This index contains all content that has been extracted from web sources and enhanced with AI processing for improved structure, summaries, and readability.

## Content Statistics

- **Total Enhanced Files**: ${files.length}
- **Processing Date**: ${new Date().toLocaleDateString()}
- **Enhancement Features**: AI summaries, structured documentation, quality scoring

## Content Categories

`;

        // Group files by category/directory
        const categories = new Map();
        
        for (const file of files) {
            try {
                const content = fs.readFileSync(file, 'utf8');
                const relativePath = path.relative(this.processedDir, file);
                const category = path.dirname(relativePath);
                
                if (!categories.has(category)) {
                    categories.set(category, []);
                }
                
                // Extract metadata
                const titleMatch = content.match(/^title:\s*["']?([^"'\n]+)["']?/m);
                const descMatch = content.match(/^description:\s*["']?([^"'\n]+)["']?/m);
                const aiProcessedMatch = content.match(/^aiProcessed:\s*true/m);
                
                const title = titleMatch ? titleMatch[1] : path.basename(file, '.md');
                const description = descMatch ? descMatch[1] : 'AI-enhanced content';
                const isAiProcessed = !!aiProcessedMatch;
                
                categories.get(category).push({
                    title,
                    description,
                    path: relativePath,
                    aiProcessed: isAiProcessed
                });
                
            } catch (error) {
                console.error(`Error indexing ${file}:`, error.message);
            }
        }
        
        // Generate category sections
        for (const [category, files] of categories.entries()) {
            const categoryName = category === '.' ? 'General' : category;
            indexContent += `\n### ${categoryName}\n\n`;
            
            files.forEach(file => {
                indexContent += `#### [${file.title}](${file.path})\n\n`;
                indexContent += `${file.description}\n\n`;
                indexContent += `**File**: \`${file.path}\`  \n`;
                indexContent += `**AI Enhanced**: ${file.aiProcessed ? '✅ Yes' : '❌ No'}  \n\n`;
            });
        }
        
        indexContent += `
## Processing Information

All content in this index has been:

1. **Extracted** from external web sources using Inform
2. **Filtered** through quality control and validation
3. **Enhanced** with AI-generated summaries and analysis
4. **Structured** for optimal readability and navigation
5. **Indexed** for easy discovery and access

## Usage Guidelines

- Each document includes source attribution and extraction metadata
- AI-generated summaries provide quick content overview
- Structured sections improve readability and navigation
- Original extracted content is preserved for reference
- Quality scores help identify the most reliable information

## Support

For questions about specific content or processing issues:

1. Check the original source URLs provided in document metadata
2. Review processing reports in the \`reports/\` directory
3. Verify content against original sources when accuracy is critical

---

*This index is automatically generated and updated with each processing run*
`;

        const indexPath = path.join(this.processedDir, 'index.md');
        fs.writeFileSync(indexPath, indexContent);
        
        console.log('✅ Enhanced content index generated');
    }

    getAllMarkdownFiles(dir) {
        const files = [];
        
        function scan(currentDir) {
            if (!fs.existsSync(currentDir)) return;
            
            const items = fs.readdirSync(currentDir, { withFileTypes: true });
            
            for (const item of items) {
                const fullPath = path.join(currentDir, item.name);
                
                if (item.isDirectory()) {
                    scan(fullPath);
                } else if (item.name.endsWith('.md') && !item.name.includes('.summary.')) {
                    files.push(fullPath);
                }
            }
        }
        
        scan(dir);
        return files;
    }
}

// Execute content processing
async function main() {
    const processor = new ContentProcessor();
    await processor.processAllContent();
}

main().catch(console.error);
INNEREOF
```

This comprehensive integration guide demonstrates how Inform and Giv work together to create an intelligent content extraction and processing pipeline. The system handles everything from initial web crawling through AI enhancement to automated Git workflows, providing teams with a powerful solution for managing external content sources and maintaining high-quality documentation.

The integration offers quality control, intelligent summarization, structured output, and comprehensive tracking - making it ideal for teams that need to aggregate and maintain large volumes of external content while ensuring quality and consistency.
