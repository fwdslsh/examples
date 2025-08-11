# Improving Build Performance for Large Sites

Optimize Unify build performance for enterprise-scale websites with thousands of pages. This guide covers advanced optimization techniques, caching strategies, and scalability patterns for maximum build efficiency.

## Overview

Unify's performance optimizations include:
- **Incremental builds** with intelligent change detection
- **Parallel processing** of pages and assets
- **Advanced caching** for components and dependencies
- **Memory optimization** for large site generation
- **Build profiling** and performance monitoring
- **Resource optimization** and compression strategies

## Understanding Build Performance

### Performance Metrics

Monitor these key build metrics:

```javascript
// Build performance monitoring
class BuildProfiler {
  constructor() {
    this.metrics = {
      totalTime: 0,
      phases: {},
      memoryUsage: {},
      fileStats: {},
      cacheHits: 0,
      cacheMisses: 0
    };
    
    this.startTime = process.hrtime.bigint();
    this.phases = new Map();
  }
  
  startPhase(name) {
    this.phases.set(name, {
      start: process.hrtime.bigint(),
      memoryStart: process.memoryUsage()
    });
  }
  
  endPhase(name) {
    const phase = this.phases.get(name);
    if (!phase) return;
    
    const duration = Number(process.hrtime.bigint() - phase.start) / 1000000; // ms
    const memoryEnd = process.memoryUsage();
    
    this.metrics.phases[name] = {
      duration,
      memoryDelta: {
        rss: memoryEnd.rss - phase.memoryStart.rss,
        heapUsed: memoryEnd.heapUsed - phase.memoryStart.heapUsed,
        heapTotal: memoryEnd.heapTotal - phase.memoryStart.heapTotal
      }
    };
    
    this.phases.delete(name);
  }
  
  recordCacheHit() {
    this.metrics.cacheHits++;
  }
  
  recordCacheMiss() {
    this.metrics.cacheMisses++;
  }
  
  recordFileStats(type, count, size) {
    this.metrics.fileStats[type] = { count, size };
  }
  
  finish() {
    this.metrics.totalTime = Number(process.hrtime.bigint() - this.startTime) / 1000000;
    this.metrics.memoryUsage = process.memoryUsage();
    
    return this.generateReport();
  }
  
  generateReport() {
    const report = {
      summary: {
        totalTime: `${this.metrics.totalTime.toFixed(2)}ms`,
        cacheEfficiency: `${((this.metrics.cacheHits / (this.metrics.cacheHits + this.metrics.cacheMisses)) * 100).toFixed(1)}%`,
        memoryPeak: `${(this.metrics.memoryUsage.heapUsed / 1024 / 1024).toFixed(2)}MB`
      },
      phases: Object.entries(this.metrics.phases).map(([name, data]) => ({
        name,
        duration: `${data.duration.toFixed(2)}ms`,
        memoryDelta: `${(data.memoryDelta.heapUsed / 1024 / 1024).toFixed(2)}MB`
      })),
      files: this.metrics.fileStats
    };
    
    return report;
  }
}

// Usage in build process
const profiler = new BuildProfiler();
profiler.startPhase('initialization');
// ... build code ...
profiler.endPhase('initialization');
```

### Build Configuration for Performance

Optimize your `unify.config.js` for large sites:

```javascript
export default {
  // Performance-optimized configuration
  performance: {
    // Parallel processing
    concurrency: {
      pages: 8,        // Number of pages to process in parallel
      assets: 4,       // Number of assets to process in parallel
      components: 12   // Number of components to process in parallel
    },
    
    // Memory management
    memory: {
      maxHeapSize: '4GB',           // Maximum heap size
      gcThreshold: 0.8,             // GC when 80% heap used
      pagePoolSize: 100,            // Pages to keep in memory
      componentPoolSize: 200        // Components to keep in memory
    },
    
    // Incremental builds
    incremental: {
      enabled: true,
      trackDependencies: true,
      cacheDirectory: '.unify/cache',
      manifestFile: '.unify/build-manifest.json'
    },
    
    // Caching strategies
    cache: {
      components: {
        enabled: true,
        ttl: 3600000,              // 1 hour
        maxSize: '500MB',
        compression: 'gzip'
      },
      
      assets: {
        enabled: true,
        ttl: 86400000,             // 24 hours
        maxSize: '1GB',
        compression: 'brotli'
      },
      
      pages: {
        enabled: true,
        ttl: 1800000,              // 30 minutes
        maxSize: '2GB',
        compression: 'gzip'
      }
    }
  },
  
  // Build optimization
  build: {
    // Output optimization
    minify: {
      html: true,
      css: true,
      js: true,
      removeComments: true,
      collapseWhitespace: true
    },
    
    // Asset optimization
    assets: {
      images: {
        optimize: true,
        formats: ['webp', 'avif'],
        quality: 85,
        progressive: true
      },
      
      css: {
        purge: true,
        minify: true,
        extractCritical: true
      },
      
      js: {
        minify: true,
        modulePreload: true,
        codesplitting: true
      }
    },
    
    // Chunking strategy
    chunks: {
      vendor: true,
      runtime: true,
      commons: true,
      maxSize: '250KB'
    }
  }
}
```

## Incremental Build System

### Dependency Tracking

Implement intelligent dependency tracking:

```javascript
// Advanced dependency tracker
class DependencyTracker {
  constructor(cacheDir) {
    this.cacheDir = cacheDir;
    this.dependencies = new Map();
    this.hashes = new Map();
    this.manifestPath = path.join(cacheDir, 'dependencies.json');
    
    this.loadManifest();
  }
  
  async loadManifest() {
    try {
      const content = await fs.readFile(this.manifestPath, 'utf-8');
      const manifest = JSON.parse(content);
      
      this.dependencies = new Map(manifest.dependencies || []);
      this.hashes = new Map(manifest.hashes || []);
    } catch (error) {
      // First build or corrupted manifest
      this.dependencies.clear();
      this.hashes.clear();
    }
  }
  
  async saveManifest() {
    const manifest = {
      dependencies: Array.from(this.dependencies.entries()),
      hashes: Array.from(this.hashes.entries()),
      timestamp: Date.now()
    };
    
    await fs.writeFile(this.manifestPath, JSON.stringify(manifest, null, 2));
  }
  
  addDependency(file, dependency) {
    if (!this.dependencies.has(file)) {
      this.dependencies.set(file, new Set());
    }
    
    this.dependencies.get(file).add(dependency);
  }
  
  async getFileHash(filePath) {
    try {
      const stats = await fs.stat(filePath);
      const content = await fs.readFile(filePath);
      
      // Combine mtime and content hash for better change detection
      const hash = crypto.createHash('sha256')
        .update(content)
        .update(stats.mtime.toISOString())
        .digest('hex');
      
      return hash;
    } catch (error) {
      return null;
    }
  }
  
  async hasChanged(filePath) {
    const currentHash = await this.getFileHash(filePath);
    const previousHash = this.hashes.get(filePath);
    
    if (currentHash !== previousHash) {
      this.hashes.set(filePath, currentHash);
      return true;
    }
    
    return false;
  }
  
  async getChangedFiles(files) {
    const changed = [];
    const unchanged = [];
    
    await Promise.all(files.map(async (file) => {
      if (await this.hasChanged(file)) {
        changed.push(file);
      } else {
        unchanged.push(file);
      }
    }));
    
    return { changed, unchanged };
  }
  
  async getDependentFiles(changedFile) {
    const dependents = new Set();
    
    // Find all files that depend on the changed file
    for (const [file, deps] of this.dependencies.entries()) {
      if (deps.has(changedFile)) {
        dependents.add(file);
        
        // Recursively find dependents of dependents
        const nestedDependents = await this.getDependentFiles(file);
        nestedDependents.forEach(dep => dependents.add(dep));
      }
    }
    
    return Array.from(dependents);
  }
  
  async calculateBuildScope(allFiles) {
    const { changed, unchanged } = await this.getChangedFiles(allFiles);
    
    if (changed.length === 0) {
      return { toBuild: [], fromCache: allFiles };
    }
    
    // Calculate all files that need rebuilding
    const toBuild = new Set(changed);
    
    for (const changedFile of changed) {
      const dependents = await this.getDependentFiles(changedFile);
      dependents.forEach(dep => toBuild.add(dep));
    }
    
    const fromCache = allFiles.filter(file => !toBuild.has(file));
    
    return {
      toBuild: Array.from(toBuild),
      fromCache
    };
  }
}
```

### Smart Caching System

Implement multi-layered caching:

```javascript
// Multi-level cache system
class BuildCache {
  constructor(options = {}) {
    this.options = {
      directory: '.unify/cache',
      maxSize: '2GB',
      compression: 'gzip',
      ttl: 3600000, // 1 hour
      ...options
    };
    
    this.memory = new Map();
    this.disk = new DiskCache(this.options.directory);
    this.compression = this.options.compression;
    
    this.stats = {
      hits: 0,
      misses: 0,
      writes: 0,
      size: 0
    };
  }
  
  generateKey(input) {
    // Create unique key from input (file path, dependencies, config)
    const hash = crypto.createHash('sha256');
    
    if (typeof input === 'string') {
      hash.update(input);
    } else {
      hash.update(JSON.stringify(input, null, 0));
    }
    
    return hash.digest('hex');
  }
  
  async get(key) {
    const cacheKey = this.generateKey(key);
    
    // Try memory cache first
    if (this.memory.has(cacheKey)) {
      this.stats.hits++;
      return this.memory.get(cacheKey);
    }
    
    // Try disk cache
    const diskResult = await this.disk.get(cacheKey);
    if (diskResult) {
      // Decompress if needed
      const result = await this.decompress(diskResult);
      
      // Cache in memory for faster access
      this.memory.set(cacheKey, result);
      this.stats.hits++;
      
      return result;
    }
    
    this.stats.misses++;
    return null;
  }
  
  async set(key, value) {
    const cacheKey = this.generateKey(key);
    
    // Store in memory
    this.memory.set(cacheKey, value);
    
    // Compress and store on disk
    const compressed = await this.compress(value);
    await this.disk.set(cacheKey, compressed);
    
    this.stats.writes++;
    this.stats.size += JSON.stringify(value).length;
    
    // Cleanup if memory gets too large
    await this.cleanupMemory();
  }
  
  async compress(data) {
    if (this.compression === 'none') return data;
    
    const serialized = JSON.stringify(data);
    
    if (this.compression === 'gzip') {
      return await gzip(serialized);
    } else if (this.compression === 'brotli') {
      return await brotliCompress(serialized);
    }
    
    return serialized;
  }
  
  async decompress(data) {
    if (this.compression === 'none') return data;
    
    let decompressed;
    
    if (this.compression === 'gzip') {
      decompressed = await gunzip(data);
    } else if (this.compression === 'brotli') {
      decompressed = await brotliDecompress(data);
    } else {
      decompressed = data;
    }
    
    return JSON.parse(decompressed.toString());
  }
  
  async cleanupMemory() {
    const maxMemorySize = 100; // Max items in memory
    
    if (this.memory.size > maxMemorySize) {
      // Remove oldest entries (simple LRU)
      const entries = Array.from(this.memory.entries());
      const toRemove = entries.slice(0, entries.length - maxMemorySize);
      
      toRemove.forEach(([key]) => {
        this.memory.delete(key);
      });
    }
  }
  
  async clear() {
    this.memory.clear();
    await this.disk.clear();
    
    this.stats = {
      hits: 0,
      misses: 0,
      writes: 0,
      size: 0
    };
  }
  
  getStats() {
    const efficiency = this.stats.hits / (this.stats.hits + this.stats.misses) * 100;
    
    return {
      ...this.stats,
      efficiency: `${efficiency.toFixed(1)}%`,
      memorySize: this.memory.size,
      sizeFormatted: `${(this.stats.size / 1024 / 1024).toFixed(2)}MB`
    };
  }
}

class DiskCache {
  constructor(directory) {
    this.directory = directory;
    this.ensureDirectory();
  }
  
  async ensureDirectory() {
    await fs.mkdir(this.directory, { recursive: true });
  }
  
  getCachePath(key) {
    // Split key into subdirectories to avoid too many files in one directory
    const subdir = key.slice(0, 2);
    return path.join(this.directory, subdir, key);
  }
  
  async get(key) {
    try {
      const cachePath = this.getCachePath(key);
      const data = await fs.readFile(cachePath);
      return data;
    } catch (error) {
      return null;
    }
  }
  
  async set(key, data) {
    const cachePath = this.getCachePath(key);
    const cacheDir = path.dirname(cachePath);
    
    await fs.mkdir(cacheDir, { recursive: true });
    await fs.writeFile(cachePath, data);
  }
  
  async clear() {
    await fs.rm(this.directory, { recursive: true, force: true });
    await this.ensureDirectory();
  }
}
```

## Parallel Processing Optimization

### Worker Pool Implementation

Process pages and components in parallel:

```javascript
// Worker pool for parallel processing
class WorkerPool {
  constructor(workerScript, poolSize = 4) {
    this.workerScript = workerScript;
    this.poolSize = poolSize;
    this.workers = [];
    this.queue = [];
    this.activeJobs = new Map();
    
    this.initializeWorkers();
  }
  
  initializeWorkers() {
    for (let i = 0; i < this.poolSize; i++) {
      const worker = new Worker(this.workerScript);
      
      worker.on('message', (result) => {
        this.handleWorkerMessage(worker.threadId, result);
      });
      
      worker.on('error', (error) => {
        this.handleWorkerError(worker.threadId, error);
      });
      
      worker.on('exit', (code) => {
        if (code !== 0) {
          console.warn(`Worker ${worker.threadId} exited with code ${code}`);
        }
      });
      
      this.workers.push({
        worker,
        busy: false,
        id: worker.threadId
      });
    }
  }
  
  async execute(task) {
    return new Promise((resolve, reject) => {
      const job = { task, resolve, reject, id: Date.now() };
      
      const availableWorker = this.workers.find(w => !w.busy);
      
      if (availableWorker) {
        this.assignJob(availableWorker, job);
      } else {
        this.queue.push(job);
      }
    });
  }
  
  assignJob(workerInfo, job) {
    workerInfo.busy = true;
    this.activeJobs.set(workerInfo.id, job);
    
    workerInfo.worker.postMessage({
      jobId: job.id,
      task: job.task
    });
  }
  
  handleWorkerMessage(workerId, result) {
    const job = this.activeJobs.get(workerId);
    if (!job) return;
    
    this.activeJobs.delete(workerId);
    
    const workerInfo = this.workers.find(w => w.id === workerId);
    if (workerInfo) {
      workerInfo.busy = false;
      
      // Process next job in queue
      if (this.queue.length > 0) {
        const nextJob = this.queue.shift();
        this.assignJob(workerInfo, nextJob);
      }
    }
    
    if (result.error) {
      job.reject(new Error(result.error));
    } else {
      job.resolve(result.data);
    }
  }
  
  handleWorkerError(workerId, error) {
    const job = this.activeJobs.get(workerId);
    if (job) {
      job.reject(error);
      this.activeJobs.delete(workerId);
    }
    
    // Mark worker as available
    const workerInfo = this.workers.find(w => w.id === workerId);
    if (workerInfo) {
      workerInfo.busy = false;
    }
  }
  
  async executeAll(tasks) {
    const promises = tasks.map(task => this.execute(task));
    return Promise.all(promises);
  }
  
  async executeBatch(tasks, batchSize = 10) {
    const results = [];
    
    for (let i = 0; i < tasks.length; i += batchSize) {
      const batch = tasks.slice(i, i + batchSize);
      const batchResults = await this.executeAll(batch);
      results.push(...batchResults);
      
      // Optional: Add delay between batches to prevent overwhelming
      if (i + batchSize < tasks.length) {
        await new Promise(resolve => setTimeout(resolve, 10));
      }
    }
    
    return results;
  }
  
  async terminate() {
    await Promise.all(this.workers.map(({ worker }) => worker.terminate()));
    this.workers = [];
    this.queue = [];
    this.activeJobs.clear();
  }
}

// Worker script for page processing
// workers/page-processor.js
const { parentPort } = require('worker_threads');
const { processPage } = require('../src/page-processor');

parentPort.on('message', async ({ jobId, task }) => {
  try {
    const result = await processPage(task);
    
    parentPort.postMessage({
      jobId,
      data: result
    });
  } catch (error) {
    parentPort.postMessage({
      jobId,
      error: error.message
    });
  }
});

// Usage in build process
const pageWorkers = new WorkerPool('./workers/page-processor.js', 8);

async function buildPages(pages) {
  const tasks = pages.map(page => ({
    type: 'page',
    path: page.path,
    content: page.content,
    config: page.config
  }));
  
  const results = await pageWorkers.executeBatch(tasks, 20);
  
  return results;
}
```

## Memory Optimization

### Memory-Efficient Processing

Optimize memory usage for large sites:

```javascript
// Memory-efficient page processor
class MemoryOptimizedProcessor {
  constructor(options = {}) {
    this.options = {
      pagePoolSize: 100,
      componentPoolSize: 200,
      gcThreshold: 0.8,
      maxHeapSize: '4GB',
      ...options
    };
    
    this.pagePool = new LRUCache(this.options.pagePoolSize);
    this.componentPool = new LRUCache(this.options.componentPoolSize);
    this.processedCount = 0;
    
    this.setupMemoryMonitoring();
  }
  
  setupMemoryMonitoring() {
    setInterval(() => {
      const usage = process.memoryUsage();
      const heapUsedGB = usage.heapUsed / 1024 / 1024 / 1024;
      const maxHeapGB = parseFloat(this.options.maxHeapSize.replace('GB', ''));
      
      if (heapUsedGB > maxHeapGB * this.options.gcThreshold) {
        console.log(`Memory usage: ${heapUsedGB.toFixed(2)}GB, forcing GC...`);
        
        // Force garbage collection
        if (global.gc) {
          global.gc();
        }
        
        // Clear caches
        this.pagePool.clear();
        this.componentPool.clear();
      }
    }, 5000);
  }
  
  async processPageBatch(pages) {
    const batchSize = Math.min(50, pages.length);
    const results = [];
    
    for (let i = 0; i < pages.length; i += batchSize) {
      const batch = pages.slice(i, i + batchSize);
      
      // Process batch
      const batchResults = await Promise.all(
        batch.map(page => this.processPage(page))
      );
      
      results.push(...batchResults);
      
      // Clear processed pages from memory
      batch.forEach(page => {
        delete page.content;
        delete page.dependencies;
      });
      
      // Progress reporting
      console.log(`Processed ${i + batch.length}/${pages.length} pages`);
      
      // Yield to event loop
      await new Promise(resolve => setImmediate(resolve));
    }
    
    return results;
  }
  
  async processPage(page) {
    try {
      // Check cache first
      const cacheKey = this.generatePageKey(page);
      const cached = this.pagePool.get(cacheKey);
      
      if (cached) {
        return cached;
      }
      
      // Process page
      const result = await this.buildPage(page);
      
      // Cache result
      this.pagePool.set(cacheKey, result);
      
      this.processedCount++;
      
      return result;
      
    } catch (error) {
      console.error(`Error processing page ${page.path}:`, error);
      throw error;
    }
  }
  
  async buildPage(page) {
    // Implement page building logic
    const components = await this.resolveComponents(page.components);
    const rendered = await this.renderPage(page, components);
    
    return {
      path: page.path,
      content: rendered,
      dependencies: page.dependencies,
      size: rendered.length
    };
  }
  
  async resolveComponents(componentRefs) {
    const components = [];
    
    for (const ref of componentRefs) {
      const cached = this.componentPool.get(ref.path);
      
      if (cached) {
        components.push(cached);
      } else {
        const component = await this.loadComponent(ref.path);
        this.componentPool.set(ref.path, component);
        components.push(component);
      }
    }
    
    return components;
  }
  
  generatePageKey(page) {
    return `${page.path}_${page.lastModified}_${page.configHash}`;
  }
  
  getStats() {
    const usage = process.memoryUsage();
    
    return {
      processedPages: this.processedCount,
      memoryUsage: {
        rss: `${(usage.rss / 1024 / 1024).toFixed(2)}MB`,
        heapUsed: `${(usage.heapUsed / 1024 / 1024).toFixed(2)}MB`,
        heapTotal: `${(usage.heapTotal / 1024 / 1024).toFixed(2)}MB`
      },
      cacheStats: {
        pages: this.pagePool.size,
        components: this.componentPool.size
      }
    };
  }
}

// LRU Cache implementation
class LRUCache {
  constructor(maxSize) {
    this.maxSize = maxSize;
    this.cache = new Map();
  }
  
  get(key) {
    if (this.cache.has(key)) {
      // Move to end (most recently used)
      const value = this.cache.get(key);
      this.cache.delete(key);
      this.cache.set(key, value);
      return value;
    }
    return null;
  }
  
  set(key, value) {
    if (this.cache.has(key)) {
      // Update existing
      this.cache.delete(key);
    } else if (this.cache.size >= this.maxSize) {
      // Remove least recently used
      const firstKey = this.cache.keys().next().value;
      this.cache.delete(firstKey);
    }
    
    this.cache.set(key, value);
  }
  
  clear() {
    this.cache.clear();
  }
  
  get size() {
    return this.cache.size;
  }
}
```

## Asset Optimization

### Advanced Asset Processing

Optimize images, CSS, and JavaScript for performance:

```javascript
// Asset optimization pipeline
class AssetOptimizer {
  constructor(options = {}) {
    this.options = {
      images: {
        quality: 85,
        formats: ['webp', 'avif'],
        progressive: true,
        responsive: true
      },
      css: {
        purge: true,
        minify: true,
        extractCritical: true
      },
      js: {
        minify: true,
        modulePreload: true,
        codesplitting: true
      },
      ...options
    };
    
    this.imageOptimizer = new ImageOptimizer(this.options.images);
    this.cssOptimizer = new CSSOptimizer(this.options.css);
    this.jsOptimizer = new JSOptimizer(this.options.js);
  }
  
  async optimizeAssets(assets) {
    const optimized = {
      images: [],
      css: [],
      js: [],
      other: []
    };
    
    await Promise.all(assets.map(async (asset) => {
      try {
        let result;
        
        if (this.isImage(asset.path)) {
          result = await this.imageOptimizer.optimize(asset);
          optimized.images.push(result);
        } else if (this.isCSS(asset.path)) {
          result = await this.cssOptimizer.optimize(asset);
          optimized.css.push(result);
        } else if (this.isJS(asset.path)) {
          result = await this.jsOptimizer.optimize(asset);
          optimized.js.push(result);
        } else {
          optimized.other.push(asset);
        }
      } catch (error) {
        console.error(`Error optimizing ${asset.path}:`, error);
        optimized.other.push(asset);
      }
    }));
    
    return optimized;
  }
  
  isImage(path) {
    return /\.(jpg|jpeg|png|gif|svg|webp|avif)$/i.test(path);
  }
  
  isCSS(path) {
    return /\.css$/i.test(path);
  }
  
  isJS(path) {
    return /\.js$/i.test(path);
  }
}

class ImageOptimizer {
  constructor(options) {
    this.options = options;
  }
  
  async optimize(image) {
    const results = [];
    
    // Original format optimization
    const optimized = await this.compressImage(image, image.format);
    results.push(optimized);
    
    // Generate modern formats
    for (const format of this.options.formats) {
      if (format !== image.format) {
        try {
          const converted = await this.convertImage(image, format);
          results.push(converted);
        } catch (error) {
          console.warn(`Failed to convert ${image.path} to ${format}:`, error);
        }
      }
    }
    
    // Generate responsive variants
    if (this.options.responsive) {
      const responsive = await this.generateResponsiveVariants(image);
      results.push(...responsive);
    }
    
    return {
      original: image,
      optimized: results,
      savings: this.calculateSavings(image, results)
    };
  }
  
  async compressImage(image, format) {
    // Implement image compression logic
    // This would use libraries like sharp, imagemin, etc.
    const compressed = await sharp(image.buffer)
      .format(format, {
        quality: this.options.quality,
        progressive: this.options.progressive
      })
      .toBuffer();
    
    return {
      path: image.path,
      format,
      buffer: compressed,
      size: compressed.length
    };
  }
  
  async convertImage(image, targetFormat) {
    const converted = await sharp(image.buffer)
      .format(targetFormat, {
        quality: this.options.quality
      })
      .toBuffer();
    
    const newPath = image.path.replace(/\.[^.]+$/, `.${targetFormat}`);
    
    return {
      path: newPath,
      format: targetFormat,
      buffer: converted,
      size: converted.length
    };
  }
  
  async generateResponsiveVariants(image) {
    const sizes = [640, 768, 1024, 1280, 1920];
    const variants = [];
    
    for (const width of sizes) {
      try {
        const resized = await sharp(image.buffer)
          .resize(width, null, {
            withoutEnlargement: true
          })
          .format(image.format, {
            quality: this.options.quality
          })
          .toBuffer();
        
        const variantPath = image.path.replace(
          /(\.[^.]+)$/,
          `_${width}w$1`
        );
        
        variants.push({
          path: variantPath,
          format: image.format,
          buffer: resized,
          size: resized.length,
          width
        });
      } catch (error) {
        console.warn(`Failed to generate ${width}w variant for ${image.path}:`, error);
      }
    }
    
    return variants;
  }
  
  calculateSavings(original, optimized) {
    const originalSize = original.size;
    const optimizedSize = optimized.reduce((total, variant) => total + variant.size, 0);
    
    return {
      original: originalSize,
      optimized: optimizedSize,
      saved: originalSize - optimizedSize,
      percentage: ((originalSize - optimizedSize) / originalSize * 100).toFixed(1)
    };
  }
}
```

## Build Performance Monitoring

### Real-time Performance Dashboard

Monitor build performance in real-time:

```javascript
// Build performance dashboard
class PerformanceDashboard {
  constructor() {
    this.metrics = {
      buildTime: 0,
      phases: {},
      throughput: 0,
      errors: [],
      warnings: []
    };
    
    this.startTime = Date.now();
    this.server = null;
  }
  
  startServer(port = 3001) {
    const express = require('express');
    const app = express();
    
    app.get('/metrics', (req, res) => {
      res.json(this.getMetrics());
    });
    
    app.get('/dashboard', (req, res) => {
      res.send(this.generateDashboardHTML());
    });
    
    this.server = app.listen(port, () => {
      console.log(`Performance dashboard: http://localhost:${port}/dashboard`);
    });
  }
  
  updateMetrics(update) {
    Object.assign(this.metrics, update);
    this.broadcast(update);
  }
  
  addPhase(name, duration, details = {}) {
    this.metrics.phases[name] = {
      duration,
      ...details,
      timestamp: Date.now()
    };
  }
  
  addError(error) {
    this.metrics.errors.push({
      message: error.message,
      stack: error.stack,
      timestamp: Date.now()
    });
  }
  
  addWarning(warning) {
    this.metrics.warnings.push({
      message: warning,
      timestamp: Date.now()
    });
  }
  
  getMetrics() {
    return {
      ...this.metrics,
      buildTime: Date.now() - this.startTime,
      timestamp: Date.now()
    };
  }
  
  generateDashboardHTML() {
    return `
    <!DOCTYPE html>
    <html>
    <head>
      <title>Unify Build Performance Dashboard</title>
      <style>
        body { font-family: Arial, sans-serif; margin: 20px; background: #f5f5f5; }
        .container { max-width: 1200px; margin: 0 auto; }
        .metric-card { background: white; padding: 20px; margin: 10px 0; border-radius: 8px; box-shadow: 0 2px 4px rgba(0,0,0,0.1); }
        .metric-value { font-size: 2em; font-weight: bold; color: #007bff; }
        .metric-label { color: #666; margin-top: 5px; }
        .phase-list { list-style: none; padding: 0; }
        .phase-item { display: flex; justify-content: space-between; padding: 10px 0; border-bottom: 1px solid #eee; }
        .error { color: #dc3545; }
        .warning { color: #ffc107; }
        .grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); gap: 20px; }
      </style>
    </head>
    <body>
      <div class="container">
        <h1>Unify Build Performance Dashboard</h1>
        
        <div class="grid">
          <div class="metric-card">
            <div class="metric-value" id="build-time">0s</div>
            <div class="metric-label">Build Time</div>
          </div>
          
          <div class="metric-card">
            <div class="metric-value" id="throughput">0</div>
            <div class="metric-label">Pages/Second</div>
          </div>
          
          <div class="metric-card">
            <div class="metric-value error" id="error-count">0</div>
            <div class="metric-label">Errors</div>
          </div>
          
          <div class="metric-card">
            <div class="metric-value warning" id="warning-count">0</div>
            <div class="metric-label">Warnings</div>
          </div>
        </div>
        
        <div class="metric-card">
          <h3>Build Phases</h3>
          <ul class="phase-list" id="phases"></ul>
        </div>
        
        <div class="metric-card">
          <h3>Recent Errors</h3>
          <div id="errors"></div>
        </div>
      </div>
      
      <script>
        function updateDashboard() {
          fetch('/metrics')
            .then(res => res.json())
            .then(data => {
              document.getElementById('build-time').textContent = 
                Math.round(data.buildTime / 1000) + 's';
              
              document.getElementById('throughput').textContent = 
                data.throughput.toFixed(1);
              
              document.getElementById('error-count').textContent = 
                data.errors.length;
              
              document.getElementById('warning-count').textContent = 
                data.warnings.length;
              
              // Update phases
              const phasesList = document.getElementById('phases');
              phasesList.innerHTML = '';
              
              Object.entries(data.phases).forEach(([name, phase]) => {
                const li = document.createElement('li');
                li.className = 'phase-item';
                li.innerHTML = \`
                  <span>\${name}</span>
                  <span>\${phase.duration.toFixed(2)}ms</span>
                \`;
                phasesList.appendChild(li);
              });
              
              // Update errors
              const errorsDiv = document.getElementById('errors');
              errorsDiv.innerHTML = '';
              
              data.errors.slice(-5).forEach(error => {
                const div = document.createElement('div');
                div.className = 'error';
                div.textContent = error.message;
                errorsDiv.appendChild(div);
              });
            })
            .catch(err => console.error('Failed to update dashboard:', err));
        }
        
        // Update every second
        setInterval(updateDashboard, 1000);
        updateDashboard();
      </script>
    </body>
    </html>
    `;
  }
  
  stop() {
    if (this.server) {
      this.server.close();
    }
  }
}
```

## Build Optimization Strategies

### Configuration Recommendations

Optimize settings based on site characteristics:

```javascript
// Intelligent configuration optimizer
class ConfigOptimizer {
  static optimize(siteMetrics) {
    const config = {
      performance: {},
      build: {},
      cache: {}
    };
    
    // Optimize based on site size
    if (siteMetrics.pageCount > 1000) {
      // Large site optimizations
      config.performance.concurrency = {
        pages: Math.min(16, os.cpus().length),
        assets: Math.min(8, os.cpus().length),
        components: Math.min(24, os.cpus().length * 2)
      };
      
      config.performance.memory = {
        maxHeapSize: '8GB',
        gcThreshold: 0.7,
        pagePoolSize: 200,
        componentPoolSize: 500
      };
      
      config.cache.components.ttl = 7200000; // 2 hours
      config.cache.pages.ttl = 3600000;     // 1 hour
    } else if (siteMetrics.pageCount > 100) {
      // Medium site optimizations
      config.performance.concurrency = {
        pages: Math.min(8, os.cpus().length),
        assets: Math.min(4, os.cpus().length),
        components: Math.min(12, os.cpus().length)
      };
      
      config.performance.memory = {
        maxHeapSize: '4GB',
        gcThreshold: 0.8,
        pagePoolSize: 100,
        componentPoolSize: 200
      };
    } else {
      // Small site optimizations
      config.performance.concurrency = {
        pages: 4,
        assets: 2,
        components: 6
      };
      
      config.performance.memory = {
        maxHeapSize: '2GB',
        gcThreshold: 0.9,
        pagePoolSize: 50,
        componentPoolSize: 100
      };
    }
    
    // Optimize based on component complexity
    if (siteMetrics.avgComponentsPerPage > 10) {
      config.cache.components.enabled = true;
      config.cache.components.maxSize = '1GB';
    }
    
    // Optimize based on asset size
    if (siteMetrics.totalAssetSize > 100 * 1024 * 1024) { // 100MB
      config.build.assets.images.optimize = true;
      config.build.assets.css.purge = true;
      config.build.assets.js.codesplitting = true;
    }
    
    return config;
  }
  
  static analyzeSite(pages, assets, components) {
    const metrics = {
      pageCount: pages.length,
      componentCount: components.length,
      assetCount: assets.length,
      totalAssetSize: assets.reduce((sum, asset) => sum + asset.size, 0),
      avgComponentsPerPage: pages.reduce((sum, page) => sum + page.components.length, 0) / pages.length,
      maxComponentsPerPage: Math.max(...pages.map(page => page.components.length)),
      avgPageSize: pages.reduce((sum, page) => sum + page.size, 0) / pages.length
    };
    
    return metrics;
  }
}

// Usage
const siteMetrics = ConfigOptimizer.analyzeSite(pages, assets, components);
const optimizedConfig = ConfigOptimizer.optimize(siteMetrics);

console.log('Recommended configuration:', optimizedConfig);
```

## Troubleshooting Performance Issues

### Performance Debugging Tools

Debug build performance issues:

```javascript
// Performance profiler for debugging
class PerformanceProfiler {
  constructor() {
    this.profiles = new Map();
    this.callStack = [];
  }
  
  start(label) {
    const profile = {
      label,
      startTime: process.hrtime.bigint(),
      startMemory: process.memoryUsage(),
      children: new Map()
    };
    
    if (this.callStack.length > 0) {
      const parent = this.callStack[this.callStack.length - 1];
      parent.children.set(label, profile);
    } else {
      this.profiles.set(label, profile);
    }
    
    this.callStack.push(profile);
  }
  
  end(label) {
    const profile = this.callStack.pop();
    
    if (!profile || profile.label !== label) {
      console.warn(`Profiler: Mismatched label "${label}"`);
      return;
    }
    
    profile.endTime = process.hrtime.bigint();
    profile.endMemory = process.memoryUsage();
    profile.duration = Number(profile.endTime - profile.startTime) / 1000000; // ms
    profile.memoryDelta = {
      rss: profile.endMemory.rss - profile.startMemory.rss,
      heapUsed: profile.endMemory.heapUsed - profile.startMemory.heapUsed
    };
  }
  
  generateReport() {
    const report = {
      summary: this.generateSummary(),
      detailed: this.generateDetailedReport(),
      recommendations: this.generateRecommendations()
    };
    
    return report;
  }
  
  generateSummary() {
    const allProfiles = this.getAllProfiles();
    const total = allProfiles.reduce((sum, p) => sum + p.duration, 0);
    
    const slowest = allProfiles
      .filter(p => p.duration > 0)
      .sort((a, b) => b.duration - a.duration)
      .slice(0, 10);
    
    return {
      totalTime: `${total.toFixed(2)}ms`,
      slowestOperations: slowest.map(p => ({
        label: p.label,
        duration: `${p.duration.toFixed(2)}ms`,
        percentage: `${(p.duration / total * 100).toFixed(1)}%`
      }))
    };
  }
  
  generateDetailedReport() {
    return Array.from(this.profiles.values()).map(profile => 
      this.formatProfile(profile, 0)
    );
  }
  
  formatProfile(profile, depth) {
    const indent = '  '.repeat(depth);
    const memoryMB = profile.memoryDelta ? 
      (profile.memoryDelta.heapUsed / 1024 / 1024).toFixed(2) : '0';
    
    const formatted = {
      label: `${indent}${profile.label}`,
      duration: `${profile.duration?.toFixed(2) || 0}ms`,
      memory: `${memoryMB}MB`,
      children: []
    };
    
    if (profile.children) {
      for (const child of profile.children.values()) {
        formatted.children.push(this.formatProfile(child, depth + 1));
      }
    }
    
    return formatted;
  }
  
  generateRecommendations() {
    const allProfiles = this.getAllProfiles();
    const recommendations = [];
    
    // Find slow operations
    const slowOperations = allProfiles.filter(p => p.duration > 1000); // > 1s
    if (slowOperations.length > 0) {
      recommendations.push({
        type: 'performance',
        message: `Found ${slowOperations.length} operations taking >1s`,
        suggestions: [
          'Consider caching slow operations',
          'Use parallel processing where possible',
          'Optimize algorithms and data structures'
        ]
      });
    }
    
    // Find memory-intensive operations
    const memoryIntensive = allProfiles.filter(p => 
      p.memoryDelta && p.memoryDelta.heapUsed > 100 * 1024 * 1024 // > 100MB
    );
    
    if (memoryIntensive.length > 0) {
      recommendations.push({
        type: 'memory',
        message: `Found ${memoryIntensive.length} operations using >100MB`,
        suggestions: [
          'Implement streaming for large data',
          'Use object pooling',
          'Clear references when done'
        ]
      });
    }
    
    return recommendations;
  }
  
  getAllProfiles() {
    const profiles = [];
    
    const collectProfiles = (profileMap) => {
      for (const profile of profileMap.values()) {
        profiles.push(profile);
        if (profile.children) {
          collectProfiles(profile.children);
        }
      }
    };
    
    collectProfiles(this.profiles);
    return profiles;
  }
}

// Usage
const profiler = new PerformanceProfiler();

profiler.start('build');
  profiler.start('loadPages');
  // ... load pages ...
  profiler.end('loadPages');
  
  profiler.start('processPages');
  // ... process pages ...
  profiler.end('processPages');
profiler.end('build');

const report = profiler.generateReport();
console.log('Performance Report:', JSON.stringify(report, null, 2));
```

## Next Steps

### Advanced Performance Topics
- [Memory Profiling and Optimization](../guides/memory-optimization.md)
- [Build Caching Strategies](../guides/build-caching.md)
- [Distributed Build Systems](../guides/distributed-builds.md)

### Monitoring and Analytics
- [Build Performance Monitoring](../integrations/build-monitoring.md)
- [Performance Metrics Collection](../integrations/performance-metrics.md)
- [Automated Performance Testing](../guides/performance-testing.md)

### Enterprise Features
- [Multi-Site Build Optimization](../guides/multi-site-builds.md)
- [CI/CD Performance Optimization](../integrations/cicd-performance.md)
- [Cloud Build Scaling](../guides/cloud-builds.md)

**Your Unify builds are now optimized for maximum performance and scalability!** ⚡

---

*This guide references the [Unify performance requirements](https://github.com/fwdslsh/unify) and [incremental build system documentation](https://github.com/fwdslsh/unify/blob/main/docs/performance.md). For the latest optimization techniques, visit [fwdslsh.dev](https://fwdslsh.dev).*