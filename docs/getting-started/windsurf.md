<!-- Copyright 2026 Dynatrace LLC

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    https://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License. -->

# Windsurf Setup

Configure OpenTelemetry observability skills for Windsurf IDE with AI-enhanced development capabilities.

## Installation

1. **Install the skills package:**

   ```bash
   npx skills add henrikrexed/observability-agent-skills
   ```

2. **Create Windsurf configuration files:**

## Windsurf Configuration

### Create .windsurfrules

Create `.windsurfrules` in your project root to configure Windsurf's AI behavior:

```yaml
# OpenTelemetry Observability Rules for Windsurf

project:
  name: "OpenTelemetry Observability Project"
  type: "observability"
  stack:
    - opentelemetry
    - kubernetes
    - dynatrace
  
skills:
  enabled:
    - otel-instrumentation
    - otel-collector  
    - otel-ottl
    - otel-semantic-conventions
    - otel-dynatrace

coding_standards:
  security_first: true
  include_error_handling: true
  follow_semantic_conventions: true
  pii_protection: mandatory
  
instrumentation:
  auto_suggest: true
  patterns:
    - security_first
    - production_ready
    - performance_optimized
  
  guidelines:
    - name: "PII Protection"
      description: "Never instrument sensitive data"
      examples:
        good:
          - "span.setAttribute('user.id', hashUserId(userId));"
          - "span.setAttribute('http.url', sanitizeUrl(url));"
        bad:
          - "span.setAttribute('user.email', user.email);"
          - "span.setAttribute('password', password);"
    
    - name: "Error Handling"
      description: "Always include proper error handling"
      template: |
        try {
          // Business logic
          span.setStatus({ code: SpanStatusCode.OK });
        } catch (error) {
          span.setStatus({ code: SpanStatusCode.ERROR, message: error.message });
          span.recordException(error);
          throw error;
        } finally {
          span.end();
        }
    
    - name: "Semantic Conventions"
      description: "Use OpenTelemetry standard attribute names"
      required_attributes:
        - "service.name"
        - "service.version"
        - "deployment.environment"
      
collector:
  configuration:
    processor_order:
      - memory_limiter  # Always first
      - k8sattributes   # Resource enrichment
      - transform       # PII cleanup
      - batch          # Always last
    
    required_processors:
      - memory_limiter
      - batch
    
    security:
      ottl_transformations: required
      pii_redaction: mandatory
      
dynatrace:
  integration:
    dql_optimization: true
    dashboard_standards: true
    slo_requirements: true
    
  query_patterns:
    - error_analysis
    - performance_monitoring
    - business_metrics

code_generation:
  preferences:
    include_comments: true
    include_types: true
    error_handling: comprehensive
    performance_considerations: true
    
  templates:
    instrumentation:
      node_js: |
        const { trace } = require('@opentelemetry/api');
        const tracer = trace.getTracer('{{service_name}}', '{{version}}');
        
        tracer.startActiveSpan('{{operation_name}}', (span) => {
          span.setAttributes({
            // Safe attributes only
          });
          
          try {
            // Business logic here
            span.setStatus({ code: SpanStatusCode.OK });
          } catch (error) {
            span.setStatus({ code: SpanStatusCode.ERROR, message: error.message });
            span.recordException(error);
            throw error;
          } finally {
            span.end();
          }
        });
      
      python: |
        from opentelemetry import trace
        
        tracer = trace.get_tracer("{{service_name}}", "{{version}}")
        
        with tracer.start_as_current_span("{{operation_name}}") as span:
            span.set_attributes({
                # Safe attributes only
            })
            
            try:
                # Business logic here
                pass
            except Exception as e:
                span.set_status(trace.Status(trace.StatusCode.ERROR, str(e)))
                span.record_exception(e)
                raise

technology_specific:
  javascript:
    frameworks:
      express:
        middleware_patterns: true
        error_handling: true
        async_context: true
      fastify:
        plugin_integration: true
        request_hooks: true
        
  python:
    frameworks:
      django:
        middleware_integration: true
        model_instrumentation: true
        async_views: true
      fastapi:
        dependency_injection: true
        async_endpoints: true
        middleware_stack: true
        
  go:
    patterns:
      context_propagation: true
      middleware_chains: true
      error_wrapping: true
      
quality_checks:
  security:
    - no_pii_in_attributes
    - sanitized_urls
    - redacted_headers
    - hashed_user_ids
    
  performance:
    - cardinality_limits
    - sampling_configuration
    - resource_limits
    - batch_processing
    
  correctness:
    - semantic_conventions
    - error_handling
    - context_propagation
    - resource_attributes
```

### Create .windsurf/project.json (Optional)

Enhanced project configuration for better AI assistance:

```json
{
  "name": "OpenTelemetry Observability",
  "version": "1.0.0",
  "description": "Production-ready OpenTelemetry implementation",
  
  "ai_assistance": {
    "code_suggestions": {
      "observability": {
        "enabled": true,
        "priority": "high",
        "skills": [
          "otel-instrumentation",
          "otel-collector",
          "otel-ottl",
          "otel-dynatrace"
        ]
      }
    },
    
    "context_awareness": {
      "project_type": "observability",
      "security_level": "strict",
      "performance_requirements": "production",
      "compliance": ["PII_protection", "GDPR", "SOC2"]
    }
  },
  
  "development": {
    "environment": "kubernetes",
    "observability_backend": "dynatrace",
    "deployment_strategy": "rolling",
    "monitoring_requirements": {
      "sla": "99.9%",
      "response_time": "<200ms",
      "error_rate": "<0.1%"
    }
  },
  
  "code_quality": {
    "instrumentation_coverage": "100%",
    "security_scan": "required",
    "performance_review": "required",
    "documentation": "comprehensive"
  }
}
```

## Testing Your Setup

### AI Code Assistance Test

Use Windsurf's AI features to request observability code:

```
Add OpenTelemetry instrumentation to this Express.js service with comprehensive error handling, PII protection, and Kubernetes resource attributes
```

Expected features in response:

- Complete SDK initialization
- Custom span creation with safe attributes
- Comprehensive error handling
- PII protection patterns
- Kubernetes-aware resource attributes

### Configuration Generation Test

Request collector configuration:

```
Generate a production-ready OpenTelemetry collector configuration for Kubernetes that exports to Dynatrace with OTTL-based PII redaction and performance optimization
```

Should generate:

- Complete YAML configuration
- Proper processor ordering
- OTTL transformation rules
- Kubernetes deployment manifests
- Performance and security optimizations

### Code Review Test

Select existing instrumentation code and use Windsurf's analysis:

```
Analyze this OpenTelemetry instrumentation for security vulnerabilities, performance issues, and compliance with semantic conventions
```

## Windsurf-Specific Features

### AI Code Suggestions

Windsurf provides real-time suggestions for:

- OpenTelemetry API usage
- Semantic convention compliance
- Security pattern implementation
- Performance optimization
- Error handling improvements

### Intelligent Refactoring

Windsurf can help refactor code to:

- Add proper instrumentation
- Implement PII protection
- Optimize for performance
- Follow semantic conventions
- Enhance error handling

### Context-Aware Documentation

Windsurf generates documentation that includes:

- Instrumentation strategy explanations
- Security consideration notes
- Performance impact analysis
- Compliance requirement coverage
- Troubleshooting guides

### Smart Code Completion

Enhanced completion for:

- OpenTelemetry attribute names
- Semantic convention values
- OTTL function names
- DQL query syntax
- Collector configuration properties

## Framework Integration

### Express.js Setup

Add to `.windsurfrules`:

```yaml
express_patterns:
  middleware:
    - request_context_enrichment
    - error_handling_middleware
    - performance_monitoring
    
  instrumentation:
    - route_level_spans
    - business_logic_metrics
    - error_categorization
    
  security:
    - url_sanitization
    - header_redaction
    - user_id_hashing
```

### Django Setup

Add to `.windsurfrules`:

```yaml
django_patterns:
  middleware:
    - request_response_correlation
    - database_query_instrumentation
    - cache_operation_tracking
    
  models:
    - orm_query_optimization
    - business_event_tracking
    - performance_monitoring
    
  views:
    - endpoint_instrumentation
    - permission_context
    - error_handling
```

## Advanced Configuration

### Multi-Service Projects

For microservices architecture:

```yaml
services:
  api_gateway:
    instrumentation_focus:
      - http_routing
      - authentication_context
      - rate_limiting_metrics
      
  user_service:
    instrumentation_focus:
      - business_operations
      - data_privacy
      - performance_metrics
      
  payment_service:
    instrumentation_focus:
      - transaction_tracking
      - fraud_detection_metrics
      - compliance_logging
      
shared_patterns:
  - error_handling
  - pii_protection
  - context_propagation
  - resource_attributes
```

### Environment-Specific Rules

Configure different rules for different environments:

```yaml
environments:
  development:
    sampling_rate: 100
    log_level: debug
    security_enforcement: strict
    
  staging:
    sampling_rate: 50
    log_level: info
    security_enforcement: strict
    
  production:
    sampling_rate: 10
    log_level: warn
    security_enforcement: strict
    performance_monitoring: enhanced
```

## Windsurf Workflow

### 1. AI-Assisted Development

1. **Write intent comments** describing observability needs
2. **Let Windsurf suggest** appropriate instrumentation
3. **Review and customize** the generated code
4. **Iterate** based on feedback and requirements

### 2. Intelligent Code Review

1. **Select code sections** for review
2. **Request specific analysis** (security, performance, compliance)
3. **Apply suggested improvements**
4. **Validate** changes meet requirements

### 3. Configuration Management

1. **Describe infrastructure needs**
2. **Generate configuration files**
3. **Customize** for specific requirements
4. **Validate** against best practices

### 4. Documentation Generation

1. **Request documentation** for implemented patterns
2. **Generate troubleshooting guides**
3. **Create deployment instructions**
4. **Maintain consistency** across services

## Performance Optimization

### AI-Suggested Optimizations

Windsurf can suggest:

```yaml
optimizations:
  sampling:
    - adaptive_sampling_based_on_load
    - error_prioritization
    - business_critical_path_emphasis
    
  cardinality_control:
    - attribute_allowlisting
    - high_cardinality_detection
    - automated_cleanup_rules
    
  resource_efficiency:
    - batch_size_optimization
    - memory_usage_monitoring
    - cpu_impact_minimization
```

### Monitoring Suggestions

```yaml
monitoring_enhancements:
  collector_health:
    - pipeline_performance_metrics
    - error_rate_monitoring
    - resource_utilization_tracking
    
  application_health:
    - instrumentation_overhead_metrics
    - trace_completion_rates
    - span_attribute_distribution
```

## Troubleshooting

### AI Not Suggesting Observability Patterns

1. Check `.windsurfrules` syntax and formatting
2. Verify skills are enabled in configuration
3. Use specific observability terminology
4. Reference OpenTelemetry explicitly in prompts

### Inconsistent Code Generation

1. Update `.windsurfrules` with more detailed guidelines
2. Include specific examples of desired patterns
3. Use technology-specific configuration sections
4. Provide more context about your environment

### Missing Security Patterns

1. Emphasize security requirements in configuration
2. Include PII protection examples
3. Set security_first to true
4. Add specific security validation rules

## Best Practices

### 1. Descriptive Intent Comments

Use clear comments to guide AI suggestions:

```javascript
// Add comprehensive OpenTelemetry instrumentation with:
// - Business context attributes
// - PII protection for user data  
// - Error handling with proper span status
// - Performance monitoring metrics
function processUserOrder(user, order) {
  // Windsurf will generate appropriate instrumentation
}
```

### 2. Technology-Specific Configuration

Customize rules for your specific technology stack:

```yaml
my_stack:
  frontend: react_with_rum
  api: express_nodejs  
  database: postgresql
  deployment: kubernetes
  observability: dynatrace_saas
```

### 3. Iterative Improvement

Continuously refine your configuration:

```yaml
# Add new patterns as you discover them
# Update security requirements based on compliance needs
# Enhance performance rules based on production learnings
# Document anti-patterns to avoid
```

### 4. Team Consistency

Share configuration across your team:

```bash
# Version control .windsurfrules
# Create team-specific templates
# Document project-specific patterns
# Regular configuration reviews
```

## Next Steps

1. ✅ Complete this setup
2. 📚 Explore the [Skills Documentation](../skills/)
3. 🧪 Try the [Example Patterns](../examples/)
4. 🎥 Learn from [IsItObservable channel](https://www.youtube.com/@IsItObservable)

Need help? [Open an issue](https://github.com/henrikrexed/observability-agent-skills/issues) or ask Windsurf AI: "How can I improve my OpenTelemetry setup for better security and performance?"
