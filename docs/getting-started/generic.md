# Generic Setup

Configure OpenTelemetry observability skills for any MCP/AgentSkills-compatible AI assistant.

## Installation

```bash
npx skills add henrikrexed/observability-agent-skills
```

## Universal Configuration

### Create .agent-skills.yaml
Create a universal configuration file that works with most AI assistants:

```yaml
# OpenTelemetry Observability Skills Configuration

project:
  name: "OpenTelemetry Observability Project"
  type: "observability"
  description: "Production-ready OpenTelemetry implementation with security and performance focus"

skills:
  source: "henrikrexed/observability-agent-skills"
  version: "1.0.0"
  
  available:
    - name: "otel-instrumentation"
      description: "SDK setup, spans, metrics, logs, PII protection"
      triggers: ["instrumentation", "tracing", "metrics", "observability"]
    
    - name: "otel-collector"
      description: "Pipeline configuration and deployment"
      triggers: ["collector", "pipeline", "deployment", "configuration"]
    
    - name: "otel-ottl"
      description: "Data transformation and filtering"
      triggers: ["ottl", "transformation", "filtering", "data processing"]
    
    - name: "otel-semantic-conventions"
      description: "Attribute standards and naming patterns"
      triggers: ["semantic conventions", "attributes", "naming"]
    
    - name: "otel-dynatrace"
      description: "DQL queries, dashboards, SLOs, workflows"
      triggers: ["dynatrace", "dql", "dashboard", "slo", "workflow"]

technology_stack:
  languages:
    - javascript  # Change to your primary language
    - python     # Add additional languages as needed
  
  frameworks:
    - express    # Web framework
    - django     # Alternative framework
  
  deployment:
    - kubernetes
    - docker
  
  observability_backend:
    - dynatrace  # Primary backend
    - otlp       # Fallback/additional

guidelines:
  security:
    pii_protection: "mandatory"
    sensitive_data_handling: "strict"
    security_first_approach: true
    
  performance:
    production_ready: true
    cardinality_control: true
    resource_optimization: true
    
  code_quality:
    error_handling: "comprehensive"
    semantic_conventions: "required"
    documentation: "inline"
    testing: "unit_and_integration"

patterns:
  instrumentation:
    - "Always include PII protection"
    - "Use semantic conventions"
    - "Implement proper error handling"
    - "Add performance monitoring"
    - "Include business context safely"
  
  collector:
    - "memory_limiter always first processor"
    - "batch always last processor"
    - "Include OTTL for PII redaction"
    - "Configure resource detection"
    - "Set appropriate timeouts"
  
  security:
    - "Never instrument credentials"
    - "Hash user identifiers"
    - "Sanitize URLs and queries"
    - "Redact authorization headers"
    - "Mask patterns in logs"

examples:
  safe_attributes:
    - "service.name"
    - "service.version"
    - "deployment.environment"
    - "http.method"
    - "http.status_code"
    - "db.system"
    - "db.operation"
  
  never_instrument:
    - "passwords"
    - "api_keys"
    - "email_addresses"
    - "credit_card_numbers"
    - "social_security_numbers"
    - "authorization_tokens"
```

## Assistant-Specific Instructions

### For Chat-Based Assistants
When interacting with your AI assistant, use these prompting patterns:

#### Basic Instrumentation Request
```
Using the observability-agent-skills, add OpenTelemetry instrumentation to this [language] [framework] service with:

1. Proper SDK initialization
2. Custom spans for business logic
3. PII protection for user data
4. Error handling with span status
5. Performance metrics

Technology context:
- Language: [Your language]
- Framework: [Your framework]  
- Deployment: [Your environment]
- Backend: [Your observability backend]
```

#### Collector Configuration Request
```
Using the otel-collector skill, generate an OpenTelemetry collector configuration for:

- Environment: Kubernetes
- Export target: Dynatrace
- Requirements: PII redaction, performance optimization
- Scale: [Your expected volume]

Include:
1. Complete YAML configuration
2. Kubernetes deployment manifests
3. OTTL transformation rules
4. Security best practices
```

#### Security Review Request
```
Using the observability-agent-skills security guidelines, review this instrumentation code for:

1. Potential PII exposure
2. Sensitive data in attributes
3. Missing sanitization
4. Compliance with security patterns
5. Performance concerns

[Paste your code here]
```

### For IDE-Integrated Assistants

#### Comment-Based Triggers
Use these comment patterns to trigger skill-based assistance:

```javascript
// @observability-agent-skills: Add comprehensive instrumentation
function processOrder(order) {
  // Assistant will generate appropriate OpenTelemetry code
}

// @otel-instrumentation: Create custom span with business context
async function validatePayment(payment) {
  // Assistant will add span creation with safe attributes
}

// @otel-security: Implement PII protection for user data
function handleUserData(userData) {
  // Assistant will generate sanitization and hashing patterns
}
```

#### Code Action Triggers
Most assistants support code actions. Select code and request:

- "Add OpenTelemetry instrumentation following observability-agent-skills patterns"
- "Review for security issues using otel-security guidelines"
- "Optimize for performance using collector best practices"

### For API-Based Integrations

If your AI assistant supports API integration, use these request patterns:

```json
{
  "request": "instrumentation",
  "context": {
    "skills": ["observability-agent-skills"],
    "language": "javascript",
    "framework": "express",
    "requirements": [
      "pii_protection",
      "error_handling", 
      "semantic_conventions"
    ]
  },
  "code": "// Your code here"
}
```

## Testing Your Setup

### Basic Functionality Test
Request basic instrumentation:

```
Add OpenTelemetry tracing to a simple HTTP API endpoint with proper error handling and security
```

Expected response elements:
- SDK initialization code
- Custom span creation
- Error handling patterns
- Security considerations
- Semantic convention usage

### Advanced Configuration Test
Request collector configuration:

```
Create a production-ready OpenTelemetry collector configuration for Kubernetes with Dynatrace export and comprehensive security
```

Expected response elements:
- Complete YAML configuration
- Processor ordering (memory_limiter first, batch last)
- OTTL transformation rules
- Security patterns
- Kubernetes deployment configuration

### Security Validation Test
Provide code with potential security issues:

```javascript
span.setAttribute('user.email', user.email);
span.setAttribute('password', request.body.password);
span.setAttribute('full_url', request.originalUrl);
```

Request review using skills. Expected feedback:
- Identification of PII exposure
- Suggestions for safe alternatives
- Hashing/sanitization patterns
- Compliance considerations

## Framework-Specific Prompts

### Node.js + Express
```
Using observability-agent-skills for Node.js Express applications:

Add comprehensive OpenTelemetry instrumentation including:
1. Auto-instrumentation setup
2. Custom business spans
3. Express middleware integration
4. Async context handling
5. PII protection for request data

Include error handling, performance monitoring, and Kubernetes resource attributes.
```

### Python + Django
```
Using observability-agent-skills for Python Django applications:

Implement OpenTelemetry observability with:
1. Django integration patterns
2. Database query instrumentation
3. Celery task tracing
4. User context handling (with privacy)
5. Structured logging correlation

Follow Django best practices and include security measures.
```

### Go Microservices
```
Using observability-agent-skills for Go microservices:

Add OpenTelemetry instrumentation with:
1. Manual instrumentation patterns
2. HTTP middleware integration
3. gRPC interceptors
4. Database operation tracking
5. Goroutine context propagation

Include error wrapping and performance optimization.
```

## Common Integration Patterns

### MCP Protocol Integration
If your assistant supports MCP (Model Context Protocol):

```json
{
  "type": "mcp_request",
  "protocol_version": "1.0",
  "skill_reference": "observability-agent-skills/otel-instrumentation",
  "context": {
    "project_type": "web_api",
    "language": "javascript",
    "security_level": "strict"
  },
  "request": "generate_instrumentation"
}
```

### AgentSkills Protocol
For AgentSkills-compatible systems:

```yaml
skills_request:
  skill: "henrikrexed/observability-agent-skills"
  sub_skill: "otel-collector"
  action: "generate_config"
  parameters:
    environment: "kubernetes"
    export_target: "dynatrace"
    security_requirements: ["pii_redaction", "compliance"]
```

## Troubleshooting

### Skills Not Recognized
1. Verify installation: `npx skills list`
2. Check skill name spelling: `henrikrexed/observability-agent-skills`
3. Use specific skill references: `otel-instrumentation`, `otel-collector`, etc.
4. Include skill context in prompts explicitly

### Generic Responses (Not Using Skills)
1. Reference skills explicitly in prompts
2. Use specific OpenTelemetry terminology
3. Include security and performance requirements
4. Mention production readiness needs

### Missing Security Patterns
1. Explicitly request PII protection
2. Reference security guidelines
3. Include compliance requirements
4. Ask for security review specifically

### Inconsistent Code Quality
1. Request production-ready patterns
2. Include error handling requirements
3. Ask for semantic convention compliance
4. Specify performance considerations

## Best Practices

### 1. Explicit Skill References
Always reference skills explicitly:

```
Using the observability-agent-skills package, specifically the otel-instrumentation skill...
```

### 2. Technology Context
Provide clear context about your stack:

```
For a Node.js Express application deployed on Kubernetes with Dynatrace observability...
```

### 3. Security Requirements
Always include security considerations:

```
Ensure PII protection, follow security best practices, and implement proper data sanitization...
```

### 4. Production Readiness
Request production-grade implementations:

```
Generate production-ready code with proper error handling, performance optimization, and monitoring...
```

### 5. Iterative Refinement
Build complexity progressively:

1. Start with basic instrumentation
2. Add custom metrics
3. Implement security measures
4. Optimize for performance
5. Add business context

## Next Steps

1. ✅ Complete this setup
2. 📚 Read the [Skills Documentation](../skills/)
3. 🧪 Practice with [Examples](../examples/)
4. 🎥 Watch [IsItObservable tutorials](https://www.youtube.com/@IsItObservable)
5. 🤝 [Contribute improvements](../contributing.md)

## Support

Need help with your specific AI assistant?

- 🐛 [Report integration issues](https://github.com/henrikrexed/observability-agent-skills/issues)
- 💬 [Ask questions in discussions](https://github.com/henrikrexed/observability-agent-skills/discussions)
- 📖 [Check the documentation](../skills/)
- 🎥 [Watch tutorial videos](https://www.youtube.com/@IsItObservable)

The observability-agent-skills are designed to work with any AI assistant that supports skills or context injection. The key is providing clear, specific prompts that reference the skills and your technology requirements.