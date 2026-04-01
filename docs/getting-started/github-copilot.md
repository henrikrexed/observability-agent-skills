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

# GitHub Copilot Setup

Configure OpenTelemetry observability skills for GitHub Copilot with enhanced context and intelligent code suggestions.

## Installation

1. **Install the skills package:**

   ```bash
   npx skills add henrikrexed/observability-agent-skills
   ```

2. **Create GitHub Copilot configuration files:**

## GitHub Copilot Configuration

### Create copilot-instructions.md

Create `copilot-instructions.md` in your project root to provide context to GitHub Copilot:

```markdown
# OpenTelemetry Observability Instructions for GitHub Copilot

## Project Context
This project implements OpenTelemetry observability with the following configuration:

### Technology Stack
- **Language**: [Node.js/Python/Go/Java/.NET]
- **Framework**: [Express/Django/Gin/Spring Boot/etc.]
- **Deployment**: Kubernetes with Helm
- **Observability Backend**: Dynatrace
- **Container Runtime**: Docker

### Available Skills
Use these skills for observability guidance:
- **otel-instrumentation**: SDK setup, spans, metrics, logs, PII protection
- **otel-collector**: Pipeline configuration and deployment  
- **otel-ottl**: Data transformation and filtering
- **otel-semantic-conventions**: Attribute standards
- **otel-dynatrace**: DQL queries, dashboards, SLOs

## Code Generation Guidelines

### ALWAYS Include in Instrumentation Code
1. **PII Protection**: Never instrument sensitive data (passwords, emails, SSNs, credit cards)
2. **Error Handling**: Set proper span status and record exceptions
3. **Semantic Conventions**: Use OpenTelemetry standard attribute names
4. **Resource Attributes**: Include service.name, service.version, deployment.environment
5. **Context Propagation**: Maintain trace context across async boundaries

### Security-First Patterns
```javascript
// ✅ GOOD: Hash user identifiers
span.setAttribute('user.id', hashUserId(userId));

// ❌ BAD: Expose PII  
span.setAttribute('user.email', user.email);

// ✅ GOOD: Sanitize URLs
span.setAttribute('http.url', sanitizeUrl(req.url));

// ❌ BAD: Expose query parameters
span.setAttribute('http.url', req.originalUrl);
```

### OpenTelemetry SDK Patterns

#### Node.js Initialization

```javascript
// Initialize before importing other modules
require('./tracing');

// tracing.js
const { NodeSDK } = require('@opentelemetry/sdk-node');
const { getNodeAutoInstrumentations } = require('@opentelemetry/auto-instrumentations-node');

const sdk = new NodeSDK({
  instrumentations: [getNodeAutoInstrumentations({
    '@opentelemetry/instrumentation-http': {
      ignoreIncomingRequestHook: (req) => req.url?.includes('/health')
    }
  })]
});
sdk.start();
```

#### Custom Span Creation

```javascript
const { trace } = require('@opentelemetry/api');
const tracer = trace.getTracer('service-name', '1.0.0');

// Business logic spans
tracer.startActiveSpan('order.process', (span) => {
  span.setAttributes({
    'order.id': orderId,
    'order.amount': amount,
    'customer.tier': customerTier
  });
  
  try {
    // Business logic here
    span.setStatus({ code: SpanStatusCode.OK });
  } catch (error) {
    span.setStatus({ code: SpanStatusCode.ERROR, message: error.message });
    span.recordException(error);
  } finally {
    span.end();
  }
});
```

### Collector Configuration Patterns

#### Processor Ordering (MANDATORY)

```yaml
processors:
  memory_limiter:    # ALWAYS FIRST
    limit_mib: 512
  k8sattributes:     # Resource enrichment  
    extract:
      metadata: [k8s.pod.name, k8s.namespace.name]
  transform:         # PII cleanup
    trace_statements:
      - context: span
        statements:
          - set(span.attributes["http.request.header.authorization"], "REDACTED") where span.attributes["http.request.header.authorization"] != nil
  batch:            # ALWAYS LAST
    timeout: 200ms
    send_batch_size: 8192
```

#### OTTL Security Transformations

```yaml
# PII Redaction Template
transform/redact-pii:
  error_mode: ignore
  trace_statements:
    - context: span
      statements:
        # Redact auth headers
        - set(span.attributes["http.request.header.authorization"], "REDACTED") where span.attributes["http.request.header.authorization"] != nil
        # Hash user IDs
        - set(span.attributes["user.email"], SHA256(span.attributes["user.email"])) where span.attributes["user.email"] != nil
  log_statements:
    - context: log
      statements:
        # Mask credit cards
        - replace_pattern(log.body["string"], "\\b(\\d{4})[\\d\\s-]{8,12}(\\d{4})\\b", "$1-****-****-$2")
```

### Kubernetes Deployment Patterns

#### Collector DaemonSet

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: otel-collector
spec:
  template:
    spec:
      containers:
      - name: otel-collector
        image: otel/opentelemetry-collector-contrib:latest
        resources:
          requests:
            memory: "256Mi"
            cpu: "100m"
          limits:
            memory: "512Mi"
            cpu: "200m"
        env:
        - name: DT_API_TOKEN
          valueFrom:
            secretKeyRef:
              name: dynatrace-tokens
              key: api-token
```

### Language-Specific Best Practices

#### Python (Django/FastAPI)

```python
# Initialize OpenTelemetry
from opentelemetry import trace
from opentelemetry.exporter.otlp.proto.grpc.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor

# Configure tracer
trace.set_tracer_provider(TracerProvider())
otlp_exporter = OTLPSpanExporter(endpoint="http://otel-collector:4317")
trace.get_tracer_provider().add_span_processor(BatchSpanProcessor(otlp_exporter))

# Custom spans
tracer = trace.get_tracer(__name__)

@tracer.start_as_current_span("user.authenticate")
def authenticate_user(request):
    span = trace.get_current_span()
    span.set_attributes({
        "user.id": hash_user_id(request.user.id),
        "auth.method": "oauth2",
        "request.ip": get_client_ip(request)
    })
```

#### Go

```go
package main

import (
    "go.opentelemetry.io/otel"
    "go.opentelemetry.io/otel/trace"
)

func processOrder(ctx context.Context, order Order) error {
    tracer := otel.Tracer("order-service")
    ctx, span := tracer.Start(ctx, "order.process")
    defer span.End()
    
    span.SetAttributes(
        attribute.String("order.id", order.ID),
        attribute.Float64("order.amount", order.Amount),
        attribute.String("customer.tier", order.Customer.Tier),
    )
    
    if err := validateOrder(ctx, order); err != nil {
        span.RecordError(err)
        span.SetStatus(codes.Error, err.Error())
        return err
    }
    
    return nil
}
```

### Dynatrace Integration Patterns

#### DQL Query Examples

```dql
// Service error rate
fetch spans
| filter span.kind == "server"
| summarize error_rate = countIf(span.status == "ERROR") * 100.0 / count(), by: {resource.attributes["service.name"]}

// API performance by endpoint  
fetch spans
| filter span.kind == "server" and isNotNull(span.attributes["http.route"])
| summarize avg_duration = avg(span.duration), by: {span.attributes["http.route"], bin(timestamp, 5m)}
```

#### Dashboard Configuration

```json
{
  "dashboardMetadata": {
    "name": "OpenTelemetry Service Overview",
    "shared": true,
    "tags": ["opentelemetry", "services"]
  },
  "tiles": [
    {
      "name": "Service Error Rate",
      "tileType": "DATA_EXPLORER",
      "query": "fetch spans | filter span.kind == \"server\" | summarize error_rate = countIf(span.status == \"ERROR\") * 100.0 / count(), by: {resource.attributes[\"service.name\"]}"
    }
  ]
}
```

### Comments for Copilot Context

When working on observability code, use these comment patterns to guide Copilot:

```javascript
// @observability-agent-skills: Add OpenTelemetry tracing with PII protection
function processUserData(userData) {
  // Copilot will generate appropriate instrumentation
}

// @otel-instrumentation: Create custom span for business logic
async function validatePayment(paymentData) {
  // Copilot will add span creation with proper attributes
}

// @otel-ottl: Generate OTTL rules to redact sensitive headers
// Copilot will create appropriate OTTL transformation rules

// @otel-dynatrace: Create DQL query for service performance analysis
// Copilot will generate optimized DQL query
```

## Error Handling Patterns

Always include proper error handling in generated code:

```javascript
try {
  // Business logic
  span.setStatus({ code: SpanStatusCode.OK });
  return result;
} catch (error) {
  span.setStatus({ 
    code: SpanStatusCode.ERROR, 
    message: error.message 
  });
  span.recordException(error);
  span.setAttributes({
    'error.type': error.constructor.name,
    'error.handled': true
  });
  throw error;
} finally {
  span.end();
}
```

## Performance Considerations

Include these patterns for production-ready code:

```yaml
# Sampling configuration
trace:
  processor:
    probabilistic_sampler:
      sampling_percentage: 10  # 10% sampling in production

# Resource limits
memory_limiter:
  limit_mib: 512
  spike_limit_mib: 128
```

```

### Create .github/copilot-workspace.yml (Optional)
For GitHub-hosted repositories, create additional context:

```yaml
# GitHub Copilot Workspace Configuration
name: OpenTelemetry Observability Project

description: |
  Production-ready OpenTelemetry implementation with comprehensive observability,
  security-first instrumentation, and Dynatrace integration.

skills:
  - observability-agent-skills

languages:
  - javascript
  - python  
  - yaml
  - dockerfile

frameworks:
  - express
  - opentelemetry

keywords:
  - opentelemetry
  - observability
  - tracing
  - metrics
  - logs
  - dynatrace
  - kubernetes
  - security
  - pii-protection

coding_style:
  security_first: true
  include_error_handling: true
  follow_semantic_conventions: true
  include_documentation: true
```

## Testing GitHub Copilot

### 1. Comment-Based Suggestions

Type comments and let Copilot generate code:

```javascript
// Add OpenTelemetry instrumentation to this Express route with proper error handling and PII protection
app.post('/api/users', async (req, res) => {
  // Copilot should suggest span creation, error handling, and safe attributes
});
```

### 2. Code Completion

Start typing OpenTelemetry code and let Copilot complete:

```javascript
const { trace } = require('@opentelemetry/api');
const tracer = trace.getTracer('my-service');

function processOrder(order) {
  return tracer.startActiveSpan('order.process', (span) => {
    // Copilot will suggest appropriate span attributes and error handling
  });
}
```

### 3. Configuration Generation

Use Copilot Chat to generate configurations:

```
Generate an OpenTelemetry collector configuration for Kubernetes that exports to Dynatrace with OTTL transformations for PII redaction
```

## GitHub Copilot Chat Integration

### Effective Chat Prompts

Use these patterns for better results:

```bash
# Instrumentation Help
/explain this OpenTelemetry instrumentation code and suggest security improvements

# Configuration Generation  
Generate a production-ready OpenTelemetry collector config for Kubernetes with Dynatrace export and PII redaction

# Code Review
Review this instrumentation code for potential sensitive data exposure and performance issues

# Debugging Help
Help me troubleshoot why traces aren't appearing in Dynatrace from my OpenTelemetry instrumented application
```

### Context-Aware Requests

Reference your project setup for better suggestions:

```bash
# Reference project context
Based on our Express.js + Kubernetes + Dynatrace setup, add OpenTelemetry instrumentation to this API endpoint

# Specify security requirements  
Add instrumentation following our PII protection guidelines in copilot-instructions.md

# Reference existing patterns
Use the same instrumentation pattern from our user service but adapt it for the payment service
```

## IDE Integration

### VS Code with Copilot

1. Install GitHub Copilot extension
2. Enable Copilot Chat
3. Use `Ctrl+I` (Cmd+I) for inline suggestions
4. Use `Ctrl+Shift+I` for chat interface

### IntelliJ with Copilot

1. Install GitHub Copilot plugin
2. Configure workspace with `copilot-instructions.md`
3. Use inline suggestions and code completion
4. Access chat via plugin sidebar

## Troubleshooting

### Copilot Not Using Skills Context

1. Verify `copilot-instructions.md` is in project root
2. Reference skills explicitly in comments
3. Use specific OpenTelemetry terminology
4. Include security requirements in prompts

### Inconsistent Code Generation

1. Update `copilot-instructions.md` with clearer guidelines
2. Use more specific comment patterns
3. Reference existing code patterns
4. Include error handling examples

### Missing Security Patterns

1. Emphasize security in `copilot-instructions.md`
2. Use explicit PII protection comments
3. Reference security best practices
4. Include negative examples (what NOT to do)

## Best Practices

### 1. Clear Comment Patterns

Use consistent comment patterns to guide Copilot:

```javascript
// @observability: Add custom span with business context and PII protection
// @security: Ensure no sensitive data in span attributes  
// @performance: Include cardinality controls for high-volume attributes
```

### 2. Reference Existing Patterns

Build consistency by referencing established patterns:

```javascript
// Use the same instrumentation pattern as userService.authenticate()
// Follow our standard error handling from paymentService.process()
```

### 3. Incremental Development

Start simple and enhance progressively:

```javascript
// Step 1: Add basic instrumentation
// Step 2: Add custom business metrics  
// Step 3: Implement PII protection
// Step 4: Add performance optimizations
```

### 4. Documentation Integration

Keep documentation updated as code evolves:

```markdown
<!-- Update copilot-instructions.md when adding new services -->
<!-- Document new instrumentation patterns -->
<!-- Include security review checklist -->
```

## Next Steps

1. ✅ Complete this setup
2. 📚 Review the [Skills Documentation](../skills/)
3. 🧪 Practice with [Example Prompts](../examples/)
4. 🎥 Learn from [IsItObservable videos](https://www.youtube.com/@IsItObservable)

Need help? [Open an issue](https://github.com/henrikrexed/observability-agent-skills/issues) or ask Copilot Chat: "How do I improve my OpenTelemetry instrumentation following security best practices?"
