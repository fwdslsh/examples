# Configuring Giv for Multiple AI Providers

Master Giv's AI provider system to work with OpenAI, Anthropic, local models, and custom endpoints. This comprehensive guide covers setup, configuration, optimization, and troubleshooting for all supported AI providers.

## Overview

Giv supports multiple AI providers for maximum flexibility:
- **OpenAI** (GPT-3.5, GPT-4, GPT-4 Turbo)
- **Anthropic** (Claude 2, Claude 3 family)
- **Local Models** (Ollama, LM Studio, OpenAI-compatible)
- **Custom Endpoints** (Self-hosted models, enterprise APIs)
- **Provider Fallbacks** for reliability and cost optimization

## Provider Configuration

### OpenAI Configuration

Configure OpenAI as your primary provider:

**`.giv.yaml`**:
```yaml
# OpenAI Configuration
api:
  provider: "openai"
  base_url: "https://api.openai.com/v1"  # Optional: custom endpoint
  api_key: "${OPENAI_API_KEY}"
  model: "gpt-4-turbo-preview"
  
  # Model parameters
  temperature: 0.7
  max_tokens: 2000
  top_p: 1.0
  frequency_penalty: 0.0
  presence_penalty: 0.0
  
  # Rate limiting
  requests_per_minute: 50
  tokens_per_minute: 40000
  
  # Retry configuration
  max_retries: 3
  retry_delay: 1000  # milliseconds
  exponential_backoff: true

# OpenAI-specific settings
openai:
  # Available models and their use cases
  models:
    commit_messages: "gpt-4-turbo-preview"
    changelogs: "gpt-4"
    release_notes: "gpt-4"
    summaries: "gpt-3.5-turbo"
    documentation: "gpt-4"
  
  # Organization and project settings (if applicable)
  organization: "${OPENAI_ORG_ID}"  # Optional
  project: "${OPENAI_PROJECT_ID}"   # Optional
  
  # Custom headers
  headers:
    "X-Custom-Header": "value"
```

**Environment Setup**:
```bash
# Set your OpenAI API key
export OPENAI_API_KEY="sk-your-api-key-here"

# Optional: Organization and project IDs
export OPENAI_ORG_ID="org-your-org-id"
export OPENAI_PROJECT_ID="proj_your-project-id"

# Verify configuration
giv config show
```

### Anthropic Configuration

Configure Anthropic Claude models:

**`.giv.yaml`**:
```yaml
# Anthropic Configuration
api:
  provider: "anthropic"
  base_url: "https://api.anthropic.com"
  api_key: "${ANTHROPIC_API_KEY}"
  model: "claude-3-opus-20240229"
  
  # Model parameters (Anthropic-specific)
  max_tokens: 4000
  temperature: 0.7
  top_p: 0.9
  top_k: 40
  
  # Rate limiting
  requests_per_minute: 60
  tokens_per_minute: 80000

# Anthropic-specific settings
anthropic:
  # Available models
  models:
    commit_messages: "claude-3-haiku-20240307"    # Fast, cost-effective
    changelogs: "claude-3-sonnet-20240229"       # Balanced performance
    release_notes: "claude-3-opus-20240229"      # Highest quality
    summaries: "claude-3-haiku-20240307"         # Quick summaries
    documentation: "claude-3-sonnet-20240229"    # Good for docs
  
  # System message customization
  system_message: |
    You are a helpful AI assistant specialized in software development.
    Focus on clarity, accuracy, and following conventional formats.
  
  # Custom headers
  headers:
    "anthropic-version": "2023-06-01"
```

**Environment Setup**:
```bash
# Set your Anthropic API key
export ANTHROPIC_API_KEY="sk-ant-your-api-key-here"

# Verify configuration
giv config show
```

### Local Models with Ollama

Configure local models using Ollama:

**Install Ollama**:
```bash
# Install Ollama
curl -fsSL https://ollama.ai/install.sh | sh

# Start Ollama service
ollama serve

# Pull models
ollama pull llama2:13b
ollama pull codellama:13b
ollama pull mistral:7b
```

**`.giv.yaml`**:
```yaml
# Ollama Configuration
api:
  provider: "ollama"
  base_url: "http://localhost:11434"
  model: "llama2:13b"
  
  # Ollama-specific parameters
  temperature: 0.8
  num_predict: 2000
  top_p: 0.9
  top_k: 40
  repeat_penalty: 1.1
  
  # No API key needed for local models
  api_key: ""

# Ollama-specific settings
ollama:
  # Model selection by task
  models:
    commit_messages: "codellama:13b"      # Good for code understanding
    changelogs: "llama2:13b"              # General language model
    release_notes: "llama2:13b"           # General language model
    summaries: "mistral:7b"               # Faster for summaries
    documentation: "codellama:13b"        # Code-aware model
  
  # Performance settings
  num_ctx: 4096        # Context window
  num_thread: 8        # CPU threads
  num_gpu: 1           # GPU layers (if available)
  
  # Timeout settings
  request_timeout: 120  # seconds
```

### Custom OpenAI-Compatible Endpoints

Configure self-hosted or third-party OpenAI-compatible APIs:

**`.giv.yaml`**:
```yaml
# Custom endpoint configuration
api:
  provider: "openai"  # Use OpenAI format
  base_url: "https://your-custom-endpoint.com/v1"
  api_key: "${CUSTOM_API_KEY}"
  model: "your-model-name"
  
  # Standard OpenAI parameters
  temperature: 0.7
  max_tokens: 2000
  
  # Custom headers for authentication
  headers:
    "Authorization": "Bearer ${CUSTOM_API_KEY}"
    "X-API-Version": "2024-01-01"

# Custom provider settings
custom:
  name: "Custom LLM Provider"
  
  # Model mappings
  models:
    commit_messages: "fast-model"
    changelogs: "balanced-model"
    release_notes: "premium-model"
  
  # Provider-specific features
  features:
    streaming: true
    function_calling: false
    image_input: false
```

## Multi-Provider Configuration

### Provider Fallback Chain

Configure multiple providers with automatic fallback:

**`.giv.yaml`**:
```yaml
# Multi-provider configuration
providers:
  - name: "primary"
    provider: "openai"
    api_key: "${OPENAI_API_KEY}"
    model: "gpt-4-turbo-preview"
    priority: 1
    
  - name: "secondary"
    provider: "anthropic"
    api_key: "${ANTHROPIC_API_KEY}"
    model: "claude-3-sonnet-20240229"
    priority: 2
    
  - name: "local"
    provider: "ollama"
    base_url: "http://localhost:11434"
    model: "llama2:13b"
    priority: 3

# Fallback strategy
fallback:
  enabled: true
  retry_attempts: 2
  fallback_on:
    - "rate_limit"
    - "api_error"
    - "timeout"
    - "model_unavailable"
  
  # Skip fallback for certain errors
  no_fallback_on:
    - "authentication_error"
    - "invalid_request"

# Load balancing (optional)
load_balancing:
  enabled: false
  strategy: "round_robin"  # round_robin, random, weighted
  weights:
    primary: 70
    secondary: 20
    local: 10
```

### Task-Specific Provider Selection

Use different providers for different tasks:

**`.giv.yaml`**:
```yaml
# Task-specific provider configuration
task_providers:
  commit_messages:
    provider: "anthropic"
    model: "claude-3-haiku-20240307"
    temperature: 0.8
    max_tokens: 200
    
  changelogs:
    provider: "openai"
    model: "gpt-4"
    temperature: 0.7
    max_tokens: 1500
    
  release_notes:
    provider: "openai"
    model: "gpt-4-turbo-preview"
    temperature: 0.6
    max_tokens: 2000
    
  summaries:
    provider: "ollama"
    model: "mistral:7b"
    temperature: 0.7
    max_tokens: 500
    
  documentation:
    provider: "anthropic"
    model: "claude-3-sonnet-20240229"
    temperature: 0.5
    max_tokens: 3000

# Default provider (fallback)
default_provider:
  provider: "openai"
  model: "gpt-3.5-turbo"
```

## Advanced Configuration

### Custom Model Parameters

Fine-tune model behavior for specific use cases:

**`.giv.yaml`**:
```yaml
# Advanced model configuration
model_configs:
  commit_messages:
    temperature: 0.8      # More creative for variety
    max_tokens: 150       # Concise messages
    top_p: 0.9
    frequency_penalty: 0.3  # Reduce repetition
    presence_penalty: 0.1
    stop_sequences: ["\n\n"]  # Stop at double newline
    
  technical_docs:
    temperature: 0.3      # More deterministic
    max_tokens: 4000      # Longer content
    top_p: 0.95
    frequency_penalty: 0.0
    presence_penalty: 0.0
    
  creative_content:
    temperature: 1.0      # Most creative
    max_tokens: 2000
    top_p: 0.95
    frequency_penalty: 0.5
    presence_penalty: 0.6

# System prompts by model type
system_prompts:
  openai: |
    You are an expert software developer and technical writer.
    Generate clear, concise, and accurate content following best practices.
    
  anthropic: |
    You are Claude, an AI assistant specialized in software development.
    Provide helpful, accurate, and well-structured responses.
    
  local: |
    You are a helpful coding assistant. Be concise and accurate.
```

### Rate Limiting and Cost Control

Implement intelligent rate limiting and cost management:

**`.giv.yaml`**:
```yaml
# Rate limiting configuration
rate_limiting:
  # Global limits
  global:
    requests_per_hour: 1000
    tokens_per_hour: 100000
    cost_per_hour: 10.00  # USD
    
  # Per-provider limits
  providers:
    openai:
      requests_per_minute: 50
      tokens_per_minute: 40000
      cost_per_day: 25.00
      
    anthropic:
      requests_per_minute: 60
      tokens_per_minute: 80000
      cost_per_day: 20.00
      
    ollama:
      requests_per_minute: 100  # No cost limits for local
      
  # Per-task limits
  tasks:
    commit_messages:
      max_requests_per_commit: 3
      max_tokens_per_request: 200
      
    release_notes:
      max_requests_per_release: 5
      max_tokens_per_request: 3000

# Cost tracking
cost_tracking:
  enabled: true
  currency: "USD"
  budget:
    daily: 50.00
    weekly: 300.00
    monthly: 1000.00
    
  alerts:
    - threshold: 0.8    # 80% of budget
      action: "warn"
    - threshold: 0.95   # 95% of budget
      action: "throttle"
    - threshold: 1.0    # 100% of budget
      action: "stop"
      
  # Cost calculation
  pricing:
    openai:
      gpt-4-turbo-preview:
        input: 0.01    # per 1K tokens
        output: 0.03   # per 1K tokens
      gpt-3.5-turbo:
        input: 0.0005
        output: 0.0015
        
    anthropic:
      claude-3-opus-20240229:
        input: 0.015
        output: 0.075
      claude-3-sonnet-20240229:
        input: 0.003
        output: 0.015
```

## Provider Management Commands

### Configuration Management

Manage provider configurations via CLI:

```bash
# List available providers
giv providers list

# Show current configuration
giv config show

# Test provider connectivity
giv providers test openai
giv providers test anthropic
giv providers test ollama

# Switch providers
giv config set provider openai
giv config set provider anthropic

# Set model for specific tasks
giv config set model.commit_messages gpt-4-turbo-preview
giv config set model.changelogs claude-3-sonnet-20240229

# Configure rate limits
giv config set rate_limit.requests_per_minute 50
giv config set rate_limit.tokens_per_minute 40000

# Test specific configurations
giv test --provider openai --model gpt-4
giv test --provider anthropic --model claude-3-opus-20240229
```

### Provider Health Monitoring

Monitor provider status and performance:

```bash
# Check provider health
giv providers health

# Show usage statistics
giv stats --provider openai --timeframe 24h
giv stats --provider anthropic --timeframe 7d

# Monitor costs
giv cost --daily
giv cost --breakdown-by-provider
giv cost --breakdown-by-task

# Performance metrics
giv performance --provider openai --task commit_messages
```

### Environment-Specific Configuration

Configure different providers for different environments:

**Development environment** (`.giv.dev.yaml`):
```yaml
api:
  provider: "ollama"
  base_url: "http://localhost:11434"
  model: "mistral:7b"

# Use local models for development
environment: "development"
cost_tracking:
  enabled: false
```

**Staging environment** (`.giv.staging.yaml`):
```yaml
api:
  provider: "openai"
  model: "gpt-3.5-turbo"
  
environment: "staging"
rate_limiting:
  requests_per_minute: 20  # Lower limits for staging
```

**Production environment** (`.giv.prod.yaml`):
```yaml
api:
  provider: "openai"
  model: "gpt-4-turbo-preview"
  
environment: "production"
rate_limiting:
  requests_per_minute: 50
cost_tracking:
  enabled: true
  alerts:
    - threshold: 0.9
      action: "warn"
```

```bash
# Use environment-specific config
giv --config .giv.dev.yaml message
giv --config .giv.prod.yaml release-notes
```

## Advanced Use Cases

### Enterprise API Configurations

Configure Giv for enterprise environments:

**`.giv.yaml`**:
```yaml
# Enterprise configuration
enterprise:
  # Proxy settings
  proxy:
    http_proxy: "http://proxy.company.com:8080"
    https_proxy: "https://proxy.company.com:8080"
    no_proxy: "localhost,127.0.0.1,.company.com"
    
  # Certificate settings
  tls:
    cert_file: "/path/to/client.crt"
    key_file: "/path/to/client.key"
    ca_file: "/path/to/ca.crt"
    insecure_skip_verify: false
    
  # Authentication
  auth:
    type: "oauth2"  # oauth2, basic, bearer
    client_id: "${OAUTH_CLIENT_ID}"
    client_secret: "${OAUTH_CLIENT_SECRET}"
    token_url: "https://auth.company.com/oauth/token"
    scopes: ["ai-api", "read", "write"]
    
  # Audit logging
  audit:
    enabled: true
    log_file: "/var/log/giv/audit.log"
    log_level: "info"
    include_content: false  # Don't log sensitive content

# Custom API endpoints
custom_endpoints:
  azure_openai:
    base_url: "https://your-resource.openai.azure.com"
    api_version: "2023-12-01-preview"
    headers:
      "api-key": "${AZURE_OPENAI_KEY}"
      
  aws_bedrock:
    base_url: "https://bedrock-runtime.us-east-1.amazonaws.com"
    auth_type: "aws_signature_v4"
    region: "us-east-1"
```

### Model Fine-tuning Integration

Integrate with fine-tuned models:

**`.giv.yaml`**:
```yaml
# Fine-tuned model configuration
fine_tuned_models:
  custom_commit_model:
    provider: "openai"
    model: "ft:gpt-3.5-turbo:your-org:commit-messages:abc123"
    use_for: ["commit_messages"]
    
  custom_docs_model:
    provider: "openai"
    model: "ft:gpt-4:your-org:documentation:def456"
    use_for: ["documentation", "summaries"]

# Training data collection (optional)
training_data:
  collect: true
  output_dir: "./training_data"
  anonymize: true
  review_required: true
```

## Troubleshooting

### Common Configuration Issues

Diagnose and fix common problems:

```bash
# Validate configuration
giv config validate

# Test connectivity
giv providers test-all

# Debug mode
export GIV_DEBUG=true
giv message --verbose

# Check authentication
giv auth check

# Verify model availability
giv models list --provider openai
giv models list --provider anthropic
```

### Error Handling and Recovery

Configure robust error handling:

**`.giv.yaml`**:
```yaml
# Error handling configuration
error_handling:
  # Retry strategy
  retry:
    max_attempts: 3
    base_delay: 1000    # milliseconds
    max_delay: 10000
    exponential_backoff: true
    jitter: true
    
  # Specific error handling
  error_actions:
    rate_limit:
      action: "wait_and_retry"
      wait_time: 60000  # 1 minute
      
    quota_exceeded:
      action: "fallback_provider"
      
    model_unavailable:
      action: "fallback_model"
      
    network_error:
      action: "retry_with_backoff"
      
    authentication_error:
      action: "fail_fast"
      message: "Check your API key configuration"

# Logging configuration
logging:
  level: "info"  # debug, info, warn, error
  file: "./giv.log"
  format: "json"  # json, text
  
  # What to log
  log_requests: true
  log_responses: false  # Don't log response content
  log_errors: true
  log_performance: true
```

### Performance Optimization

Optimize provider performance:

```bash
# Performance tuning
giv config set timeout.request 30000    # 30 seconds
giv config set timeout.total 120000     # 2 minutes
giv config set concurrency.max 5        # Max concurrent requests
giv config set cache.enabled true       # Enable response caching
giv config set cache.ttl 3600          # 1 hour cache

# Monitor performance
giv performance --show-latency
giv performance --show-token-usage
giv performance --show-cost-breakdown
```

## Best Practices

### Security Considerations

1. **API Key Management**:
   - Use environment variables for API keys
   - Rotate keys regularly
   - Use different keys for different environments
   - Never commit keys to version control

2. **Network Security**:
   - Use HTTPS for all API calls
   - Configure proper proxy settings
   - Validate SSL certificates

3. **Audit and Compliance**:
   - Enable audit logging
   - Monitor API usage
   - Review generated content
   - Implement approval workflows for sensitive repositories

### Cost Optimization

1. **Model Selection**:
   - Use cheaper models for simple tasks
   - Reserve premium models for critical tasks
   - Implement token limits per task

2. **Caching Strategy**:
   - Cache frequently used prompts
   - Implement intelligent cache invalidation
   - Share cache across team members

3. **Provider Strategy**:
   - Use local models for development
   - Implement cost-based fallbacks
   - Monitor and optimize usage patterns

### Performance Best Practices

1. **Configuration Optimization**:
   - Choose appropriate timeout values
   - Configure reasonable rate limits
   - Use provider-specific optimizations

2. **Error Handling**:
   - Implement proper retry logic
   - Use circuit breakers for failing providers
   - Configure meaningful fallbacks

3. **Monitoring and Alerting**:
   - Monitor provider health
   - Set up cost alerts
   - Track performance metrics

## Next Steps

### Advanced Provider Integration
- [Custom Provider Development](custom-providers.md)
- [Enterprise API Integration](enterprise-apis.md)
- [Provider Performance Monitoring](provider-monitoring.md)

### Team Workflows
- [Team Collaboration with Multiple Providers](../giv/team-collaboration.md)
- [CI/CD Integration with Provider Fallbacks](../integrations/cicd-provider-config.md)
- [Cost Management for Teams](../giv/cost-management.md)

### Fine-tuning and Customization
- [Model Fine-tuning for Your Codebase](model-fine-tuning.md)
- [Custom Prompt Engineering](../giv/custom-prompts.md)
- [Provider-Specific Optimizations](provider-optimization.md)

**Your Giv setup is now optimized for multiple AI providers with robust fallback and cost control!** 🤖

---

*This guide references the [Giv API configuration documentation](https://github.com/fwdslsh/giv) and [app-spec.md provider configuration](https://github.com/fwdslsh/giv/blob/main/docs/app-spec.md). For the latest provider support and configuration options, visit [fwdslsh.dev](https://fwdslsh.dev).*