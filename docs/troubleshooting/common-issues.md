# Common Issues & Solutions

This troubleshooting guide covers the most frequently encountered issues when using fwdslsh tools and their solutions.

## General Issues

### Installation Problems

#### Issue: "command not found" after installation

**Symptoms:**
```bash
$ unify --version
bash: unify: command not found
```

**Solutions:**

1. **Check PATH (for global npm/bun installs):**
   ```bash
   # Check if npm global bin is in PATH
   echo $PATH | grep -q "$(npm config get prefix)/bin" && echo "npm bin in PATH" || echo "npm bin NOT in PATH"
   
   # Add to PATH if missing
   export PATH="$(npm config get prefix)/bin:$PATH"
   
   # For Bun
   export PATH="$HOME/.bun/bin:$PATH"
   ```

2. **Verify installation location:**
   ```bash
   # Find where the tool was installed
   which unify
   npm list -g @fwdslsh/unify
   bun list -g @fwdslsh/unify
   ```

3. **Reinstall with correct method:**
   ```bash
   # Clean reinstall
   npm uninstall -g @fwdslsh/unify
   npm install -g @fwdslsh/unify
   
   # Or use npx
   npx @fwdslsh/unify --version
   ```

#### Issue: Permission denied during global installation

**Symptoms:**
```bash
$ npm install -g @fwdslsh/unify
EACCES: permission denied, mkdir '/usr/local/lib/node_modules'
```

**Solutions:**

1. **Use a Node version manager (recommended):**
   ```bash
   # Install nvm
   curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
   
   # Install latest Node
   nvm install node
   nvm use node
   
   # Now install tools
   npm install -g @fwdslsh/unify
   ```

2. **Configure npm to use a different directory:**
   ```bash
   mkdir ~/.npm-global
   npm config set prefix '~/.npm-global'
   echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc
   source ~/.bashrc
   ```

3. **Use npx instead of global install:**
   ```bash
   npx @fwdslsh/unify serve
   ```

### Network and Proxy Issues

#### Issue: Network timeouts or connection refused

**Symptoms:**
```bash
$ inform https://example.com
Error: connect ETIMEDOUT
```

**Solutions:**

1. **Check network connectivity:**
   ```bash
   # Test basic connectivity
   curl -I https://example.com
   ping google.com
   ```

2. **Configure proxy settings:**
   ```bash
   # Set proxy environment variables
   export HTTP_PROXY=http://proxy.company.com:8080
   export HTTPS_PROXY=http://proxy.company.com:8080
   export NO_PROXY=localhost,127.0.0.1,.local
   
   # For npm
   npm config set proxy http://proxy.company.com:8080
   npm config set https-proxy http://proxy.company.com:8080
   ```

3. **Use different DNS servers:**
   ```bash
   # Temporarily use different DNS
   sudo echo "nameserver 8.8.8.8" > /etc/resolv.conf.backup
   sudo echo "nameserver 8.8.8.8" > /etc/resolv.conf
   ```

## Unify-Specific Issues

### Build and Development Issues

#### Issue: Build fails with "Module not found"

**Symptoms:**
```bash
$ unify build
Error: Cannot find module '@fwdslsh/unify'
```

**Solutions:**

1. **Check local vs global installation:**
   ```bash
   # Check if installed locally
   ls node_modules/.bin/unify
   
   # Use local installation
   npx unify build
   # or
   ./node_modules/.bin/unify build
   ```

2. **Reinstall dependencies:**
   ```bash
   rm -rf node_modules package-lock.json
   npm install
   ```

3. **Check Bun installation:**
   ```bash
   # Verify Bun is installed
   bun --version
   
   # Reinstall if needed
   curl -fsSL https://bun.sh/install | bash
   ```

#### Issue: SSI includes not working

**Symptoms:**
```html
<!-- Include directive appears in output -->
<!--#include virtual="/.components/header.html" -->
```

**Solutions:**

1. **Check file paths:**
   ```bash
   # Verify file exists
   ls src/.components/header.html
   
   # Use absolute paths from src root
   <!--#include virtual="/.components/header.html" -->
   ```

2. **Check file permissions:**
   ```bash
   # Ensure files are readable
   chmod 644 src/.components/*.html
   ```

3. **Verify SSI syntax:**
   ```html
   <!-- Correct syntax -->
   <!--#include virtual="/.components/header.html" -->
   <!--#include file="header.html" -->
   
   <!-- Alternative syntax -->
   <include src="/.components/header.html"></include>
   ```

#### Issue: Live reload not working

**Symptoms:**
- Browser doesn't refresh when files change
- Development server starts but changes don't appear

**Solutions:**

1. **Check firewall/antivirus:**
   ```bash
   # Try different port
   unify serve --port 3001
   
   # Check if port is blocked
   telnet localhost 3000
   ```

2. **Clear browser cache:**
   ```bash
   # Hard refresh
   Ctrl+Shift+R (Chrome/Firefox)
   Cmd+Shift+R (Mac)
   ```

3. **Check file watching:**
   ```bash
   # Increase file watch limit (Linux)
   echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf
   sudo sysctl -p
   ```

### Output and Assets Issues

#### Issue: CSS/JS files not loading

**Symptoms:**
- Styles not applied
- JavaScript not executing
- 404 errors for assets

**Solutions:**

1. **Check asset paths:**
   ```html
   <!-- Use absolute paths -->
   <link rel="stylesheet" href="/styles/main.css">
   <script src="/js/main.js"></script>
   
   <!-- Not relative paths -->
   <link rel="stylesheet" href="styles/main.css">
   ```

2. **Verify base URL configuration:**
   ```bash
   # For GitHub Pages
   unify build --base-url https://username.github.io/repo-name
   
   # For custom domain
   unify build --base-url https://yourdomain.com
   ```

3. **Check file structure:**
   ```
   src/
   ├── styles/
   │   └── main.css
   ├── js/
   │   └── main.js
   └── index.html
   ```

## Inform-Specific Issues

### Crawling and Content Issues

#### Issue: No content extracted from pages

**Symptoms:**
- Empty markdown files generated
- Only metadata in output files

**Solutions:**

1. **Check if site uses JavaScript rendering:**
   ```bash
   # Test with curl to see raw HTML
   curl -s https://example.com | grep -i "main\|content\|article"
   
   # For SPA sites, you may need different tools
   inform --help  # Check for SPA options
   ```

2. **Test with single page first:**
   ```bash
   # Debug single page
   inform https://example.com/specific-page --max-pages 1 --verbose
   ```

3. **Check robots.txt compliance:**
   ```bash
   # Check if crawling is allowed
   curl https://example.com/robots.txt
   ```

#### Issue: Rate limiting or blocking

**Symptoms:**
```bash
HTTP 429 Too Many Requests
HTTP 403 Forbidden
Connection refused
```

**Solutions:**

1. **Reduce crawling speed:**
   ```bash
   # Conservative approach
   inform https://example.com \
     --delay 5000 \
     --concurrency 1 \
     --max-pages 10
   ```

2. **Use different User-Agent:**
   ```bash
   # Some sites block default user agents
   inform https://example.com --user-agent "Mozilla/5.0 (compatible; DocumentationBot/1.0)"
   ```

3. **Respect rate limits:**
   ```bash
   # Wait between requests
   for url in url1 url2 url3; do
     inform $url --max-pages 10
     sleep 300  # 5 minute pause
   done
   ```

#### Issue: Large files or binary content errors

**Symptoms:**
- Out of memory errors
- Corrupted output files

**Solutions:**

1. **Exclude binary files:**
   ```bash
   inform https://example.com \
     --exclude "*.pdf" \
     --exclude "*.zip" \
     --exclude "*/downloads/*"
   ```

2. **Limit file size:**
   ```bash
   # Custom crawling script with size limits
   inform https://example.com --max-file-size 1MB
   ```

3. **Use streaming for large sites:**
   ```bash
   # Process in chunks
   inform https://example.com --max-pages 50
   # Then continue from where you left off
   ```

## Giv-Specific Issues

### AI and API Issues

#### Issue: API key not found or invalid

**Symptoms:**
```bash
$ giv message
Error: API key not found or invalid
```

**Solutions:**

1. **Check environment variables:**
   ```bash
   # Verify API key is set
   echo $OPENAI_API_KEY
   echo $ANTHROPIC_API_KEY
   
   # Set in current session
   export OPENAI_API_KEY="your-key-here"
   
   # Add to shell profile
   echo 'export OPENAI_API_KEY="your-key-here"' >> ~/.bashrc
   source ~/.bashrc
   ```

2. **Check configuration:**
   ```bash
   # View current config
   giv config list
   
   # Set API configuration
   giv config set api.url "https://api.openai.com/v1/chat/completions"
   giv config set api.model "gpt-4"
   ```

3. **Test API connectivity:**
   ```bash
   # Test with curl
   curl -H "Authorization: Bearer $OPENAI_API_KEY" \
        https://api.openai.com/v1/models
   ```

#### Issue: Poor quality or empty responses

**Symptoms:**
- Generic or unhelpful commit messages
- Empty responses from AI

**Solutions:**

1. **Increase context:**
   ```bash
   # Include more information
   giv message --include-diff --max-commits 10
   
   # Check if changes are staged
   git status
   git add .
   ```

2. **Customize templates:**
   ```bash
   # Initialize templates
   giv init
   
   # Edit templates
   nano .giv/templates/commit_message_prompt.md
   ```

3. **Try different models:**
   ```bash
   # Use a different model
   giv config set api.model "gpt-3.5-turbo"
   giv config set api.model "claude-3-5-sonnet-20241022"
   ```

### Git Integration Issues

#### Issue: "Not a git repository"

**Symptoms:**
```bash
$ giv message
Error: Not a git repository
```

**Solutions:**

1. **Check current directory:**
   ```bash
   # Verify you're in a git repo
   git status
   
   # Initialize if needed
   git init
   ```

2. **Check git repository health:**
   ```bash
   # Verify .git directory exists
   ls -la .git
   
   # Check repository integrity
   git fsck
   ```

#### Issue: No changes to analyze

**Symptoms:**
```bash
$ giv message
Error: No staged changes found
```

**Solutions:**

1. **Stage changes first:**
   ```bash
   # Check what's changed
   git status
   
   # Stage changes
   git add .
   # or stage specific files
   git add src/file.js
   ```

2. **Check for uncommitted changes:**
   ```bash
   # See unstaged changes
   git diff
   
   # See staged changes
   git diff --cached
   ```

## Performance Issues

### Memory and Resource Issues

#### Issue: Out of memory during large operations

**Symptoms:**
```bash
FATAL ERROR: Ineffective mark-compacts near heap limit
JavaScript heap out of memory
```

**Solutions:**

1. **Increase Node.js memory limit:**
   ```bash
   export NODE_OPTIONS="--max-old-space-size=4096"
   
   # Or run with increased memory
   node --max-old-space-size=4096 $(which unify) build
   ```

2. **Process in smaller chunks:**
   ```bash
   # For Inform
   inform https://example.com --max-pages 25
   
   # For Unify with large sites
   unify build --incremental
   ```

3. **Clean up temporary files:**
   ```bash
   # Clear npm cache
   npm cache clean --force
   
   # Clear Bun cache
   bun cache clean
   
   # Clear system temp
   rm -rf /tmp/unify-* /tmp/inform-*
   ```

### Slow Performance

#### Issue: Very slow build times

**Solutions:**

1. **Enable parallel processing:**
   ```bash
   # Use more workers
   unify build --workers 4
   
   # For Inform
   inform https://example.com --concurrency 5
   ```

2. **Optimize assets:**
   ```bash
   # Remove large unused files
   find src -name "*.psd" -o -name "*.ai" -delete
   
   # Optimize images before build
   imageoptim src/images/*
   ```

3. **Use incremental builds:**
   ```bash
   # Only rebuild changed files
   unify build --incremental
   ```

## Getting Help

### Debugging Tips

1. **Enable verbose logging:**
   ```bash
   DEBUG=* unify build
   DEBUG=inform:* inform https://example.com
   giv --debug message
   ```

2. **Check version compatibility:**
   ```bash
   # Check all versions
   node --version
   npm --version
   bun --version
   unify --version
   inform --version
   giv --version
   ```

3. **Test with minimal example:**
   ```bash
   # Create minimal test case
   mkdir test-case
   cd test-case
   echo "<h1>Test</h1>" > src/index.html
   unify build
   ```

### Community Resources

1. **GitHub Discussions:**
   - [fwdslsh Community](https://github.com/orgs/fwdslsh/discussions)
   - Search existing discussions before posting

2. **Issue Reporting:**
   - [Unify Issues](https://github.com/fwdslsh/unify/issues)
   - [Inform Issues](https://github.com/fwdslsh/inform/issues)
   - [Giv Issues](https://github.com/fwdslsh/giv/issues)

3. **Documentation:**
   - [Complete Documentation](../README.md)
   - [Individual Tool Guides](../)

### Creating Good Bug Reports

When reporting issues, include:

1. **Environment information:**
   ```bash
   # System info
   uname -a
   node --version
   npm --version
   
   # Tool versions
   unify --version
   inform --version
   giv --version
   ```

2. **Reproduction steps:**
   ```bash
   # Exact commands that cause the issue
   unify build --pretty-urls
   ```

3. **Expected vs actual behavior:**
   - What you expected to happen
   - What actually happened
   - Any error messages

4. **Minimal reproduction case:**
   - Smallest possible example that demonstrates the issue
   - Include relevant files and configuration

## Quick Reference

### Emergency Fixes

```bash
# Reset tool to defaults
rm -rf node_modules package-lock.json
npm install

# Clear all caches
npm cache clean --force
bun cache clean

# Reinstall everything
npm uninstall -g @fwdslsh/unify @fwdslsh/inform
npm install -g @fwdslsh/unify @fwdslsh/inform

# Check system requirements
node --version  # Should be 14+
npm --version   # Should be 6+
```

### Common Commands

```bash
# Test individual tools
unify --version && echo "Unify OK"
inform --version && echo "Inform OK" 
giv --version && echo "Giv OK"

# Basic operations
unify serve --port 3001
inform https://example.com --max-pages 5
giv config list
```

Remember: When in doubt, start with the simplest possible case and gradually add complexity until you identify the source of the issue! 🔧