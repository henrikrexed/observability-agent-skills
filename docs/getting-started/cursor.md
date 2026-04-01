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

# Cursor Setup

Configure OpenTelemetry observability skills for Cursor IDE with enhanced AI assistance.

## Installation

1. **Install the skills package:**

   ```bash
   npx skills add henrikrexed/observability-agent-skills
   ```

2. **Create Cursor configuration files:**

## Cursor Configuration

### Create .cursorrules

Create `.cursorrules` in your project root to configure Cursor's AI behavior:

```markdown
# OpenTelemetry Observability Rules

## Project Context
This project uses OpenTelemetry for observability with the following stack:
- Language: [Your language - Node.js, Python, Go, Java, .NET]
- Framework: [Your framework - Express, Django, Gin, Spring Boot, etc.]
- Deployment: Kubernetes
- Observability Backend: Dynatrace

## Available Skills
- @otel-instrumentation: SDK setup, spans, metrics, logs, PII protection
- @otel-collector: Pipeline configuration and deployment  
- @otel-ottl: Data transformation and filtering
- @otel-semantic-conventions: Attribute standards
- @otel-dynatrace: DQL queries, dashboards, SLOs

## Code Generation Rules

### Always Include
1. **PII Protection**: Never instrument credentials, emails, or sensitive data
2. **Error Handling**: Proper span status and exception recording
3. **Semantic Conventions**: Use OpenTelemetry standard attribute names
4. **Resource Attributes**: Include service.name, service.version, deployment.environment
5. **Context Propagation**: Maintain trace context in async operations

### Instrumentation Patterns
- Use auto-instrumentation as the foundation
- Add custom spans for business logic only
- Implement defensive programming (null checks, type validation)
- Include cardinality controls for high-volume attributes
- Sanitize URLs and database queries

### Collector Configuration
- Always place memory_limiter first in processor chain
- Use batch processor last in chain
- Include OTTL transformations for PII redaction
- Configure proper resource detection for Kubernetes
- Set appropriate timeouts and retry policies

### Security Requirements
- Hash user identifiers when correlation is needed
- Redact authorization headers and cookies
- Mask patterns in log messages (credit cards, SSNs)
- Remove sensitive attributes before export
- Implement allowlists for safe attributes only

### Performance Considerations
- Use sampling for high-volume environments
- Limit attribute count and string lengths
- Prefer cheaper operations in hot paths
- Monitor instrumentation overhead
- Implement graceful degradation

### Dynatrace Integration
- Use delta metrics for counters
- Follow DQL best practices for efficient queries
- Create meaningful dashboard layouts
- Define SLOs with business context
- Implement proper alerting thresholds

## Code Style Preferences
- Include comprehensive error handling
- Add inline comments explaining observability choices
- Use TypeScript/Python type hints where applicable
- Follow language-specific naming conventions
- Implement proper logging correlation

## Testing Requirements
- Include unit tests for custom instrumentation
- Validate span attributes and status
- Test error scenarios and exception handling
- Verify PII protection works correctly
- Check context propagation in async flows
```

### Create .cursor/settings.json (Optional)

Enhance Cursor's AI features with project-specific settings:

```json
{
  "cursor.cpp.enableIntelliSense": true,
  "cursor.ai.enableAutoComplete": true,
  "cursor.ai.enableCodeActions": true,
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll": true,
    "source.organizeImports": true
  },
  "files.associations": {
    "*.ottl": "yaml",
    "otel-collector.yaml": "yaml"
  },
  "yaml.schemas": {
    "https://raw.githubusercontent.com/open-telemetry/opentelemetry-collector/main/cmd/configschema/configschema.json": [
      "**/otel-collector*.yaml",
      "**/collector*.yaml"
    ]
  }
}
```

## Testing Your Setup

### Basic Instrumentation Test

Use Cursor's chat feature with `@`:

```
@observability Add OpenTelemetry tracing to this Express.js API with proper error handling and PII protection
```

Expected features:

- SDK initialization with environment configuration
- Custom span creation with semantic attributes
- Error handling with proper span status
- PII protection for user data
- Resource attribute configuration

### Collector Configuration Test

In Cursor chat:

```
@observability Create a Kubernetes-ready OpenTelemetry collector config that exports to Dynatrace with PII redaction
```

Should generate:

- Complete collector configuration YAML
- Kubernetes deployment manifests
- OTTL transformation rules for security
- Dynatrace exporter with authentication
- Resource detection for Kubernetes

### Code Review Test

Select existing code and use Cursor's context menu:

```
Review this instrumentation for security issues and suggest improvements
```

Cursor should identify:

- Potential sensitive data exposure
- Missing error handling
- Semantic convention violations
- Performance concerns
- Security best practices

## Cursor-Specific Features

### @ Symbol Integration

Reference skills directly in your conversations:

```bash
@otel-instrumentation          # Get instrumentation help
@otel-collector               # Collector configuration
@otel-ottl                   # OTTL transformation rules
@otel-dynatrace              # Dynatrace integration
```

### Inline Code Actions

Cursor provides code actions for:

- Adding OpenTelemetry instrumentation
- Implementing PII protection
- Fixing semantic convention issues
- Optimizing performance
- Adding error handling

### Auto-Complete Enhancement

The skills enhance auto-complete for:

- OpenTelemetry API calls
- Semantic convention attributes
- OTTL function names
- DQL query syntax
- Configuration properties

### Code Generation

Use Cursor's generate feature with observability context:

1. **Select code block**
2. **Press Ctrl+K (Cmd+K on Mac)**
3. **Type request:** "Add OpenTelemetry instrumentation following our project patterns"
4. **Cursor generates** context-aware code

## Framework-Specific Setup

### Node.js + Express

Add to `.cursorrules`:

```markdown
## Node.js Specifics
- Use @opentelemetry/auto-instrumentations-node as foundation
- Initialize tracing before importing other modules
- Use environment variables for configuration
- Implement middleware for request context
- Handle async context propagation correctly

### Express.js Patterns
- Add custom spans for business logic only
- Use res.locals for safe request context
- Implement error handling middleware
- Sanitize req.originalUrl for spans
- Hash user IDs from req.user
```

### Python + Django

Add to `.cursorrules`:

```markdown
## Python Specifics
- Use opentelemetry-distro for auto-instrumentation
- Configure logging integration with structlog
- Use Django middleware for request context
- Implement custom middleware for span enrichment
- Handle sync/async view differences

### Django Patterns
- Add custom spans in view functions and model methods
- Use request.user safely in span attributes
- Implement database query sanitization
- Configure logging with trace correlation
- Use Django's cache framework integration
```

## Advanced Configuration

### Multi-Language Projects

For projects with multiple languages, organize rules by directory:

```markdown
## Language-Specific Rules

### /backend (Node.js)
- Use auto-instrumentations-node
- Express.js middleware patterns
- Async context handling

### /api (Python)
- Django/FastAPI patterns  
- SQLAlchemy instrumentation
- Celery task tracing

### /services (Go)
- Manual instrumentation approach
- Goroutine context propagation
- HTTP middleware patterns
```

### Microservices Architecture

Configure service-specific guidance:

```markdown
## Service Architecture

### API Gateway
- Focus on HTTP instrumentation
- Implement rate limiting metrics
- Add authentication context safely
- Monitor upstream service calls

### Business Services
- Add custom business metrics
- Instrument domain operations
- Implement saga pattern tracing
- Monitor external dependencies

### Data Services
- Focus on database performance
- Implement query optimization
- Monitor connection pools
- Track data processing metrics
```

## Cursor Workflow Integration

### 1. Code Generation Workflow

1. **Write comment** describing what you want
2. **Use Cursor Chat** with `@observability` reference
3. **Review generated code** for security and best practices
4. **Iterate** with specific feedback

### 2. Code Review Workflow

1. **Select suspicious code** with potential observability issues
2. **Right-click** → "Ask Cursor"
3. **Request review** for security, performance, or correctness
4. **Apply suggested fixes**

### 3. Documentation Workflow

1. **Select configuration file**
2. **Ask Cursor** to explain the configuration
3. **Request improvements** based on best practices
4. **Generate inline documentation**

## Troubleshooting

### Skills Not Recognized

1. Verify installation: `npx skills list`
2. Check `.cursorrules` syntax and formatting
3. Restart Cursor IDE
4. Clear Cursor cache if needed

### Missing Context

1. Ensure `.cursorrules` is in project root
2. Be specific about your technology stack
3. Use `@` references to activate skills
4. Provide code context in requests

### Inconsistent Responses

1. Update `.cursorrules` with more specific requirements
2. Use more detailed prompts
3. Reference specific skills by name
4. Provide examples of desired patterns

## Best Practices

### 1. Specific Skill References

Instead of generic requests, use specific skill references:

```
# Generic (less effective)
Help me with observability

# Specific (better)
@otel-instrumentation Add tracing to this HTTP handler with semantic conventions and PII protection
```

### 2. Context-Rich Prompts

Provide clear context about your environment:

```
@otel-collector Create a collector config for our Kubernetes environment that processes 10k spans/sec and exports to Dynatrace with OTTL PII redaction
```

### 3. Iterative Development

Start simple and enhance progressively:

1. Basic instrumentation
2. Add custom metrics
3. Implement security measures
4. Optimize performance
5. Add business context

### 4. Regular Rule Updates

Update `.cursorrules` as your project evolves:

```markdown
## Recent Updates
- Migrated from Jaeger to Dynatrace
- Added custom SLO monitoring
- Implemented OTTL-based PII redaction
- Enhanced error categorization
```

## Next Steps

1. ✅ Complete this setup
2. 📚 Explore the [Skills Documentation](../skills/)
3. 🧪 Try the [Example Prompts](../examples/)
4. 🎥 Watch [IsItObservable videos](https://www.youtube.com/@IsItObservable)

Need help? [Open an issue](https://github.com/henrikrexed/observability-agent-skills/issues) or ask in Cursor Chat: `@observability How do I troubleshoot my OpenTelemetry setup?`
