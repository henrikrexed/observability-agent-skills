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

# OpenTelemetry Instrumentation Skill

This skill provides comprehensive guidance for instrumenting applications with OpenTelemetry SDKs. It covers all three signal types (traces, metrics, logs) with security-first practices.

## What You'll Learn

- **SDK Setup**: Proper initialization and configuration
- **Spans**: Creating meaningful traces with proper naming and attributes  
- **Metrics**: Choosing instruments and controlling cardinality
- **Logs**: Structured logging with trace correlation
- **Security**: PII protection and sensitive data handling
- **SDK-Specific**: Patterns for Node.js, Python, Go, Java, and .NET

## Key Features

### 🔒 Security Built-In

All patterns include PII protection and secure attribute handling by default.

### 📊 Production-Ready

Patterns tested in high-scale production environments.

### 🎯 Semantic Conventions

Follows OpenTelemetry semantic conventions for consistent telemetry.

### 🏗️ Framework Coverage

Specific guidance for popular frameworks in each language.

## Quick Examples

### Express.js API Tracing

```javascript
app.use((req, res, next) => {
  const span = trace.getActiveSpan();
  if (span) {
    // Safe attributes only
    span.setAttributes({
      'user.id': hashUserId(req.user?.id),
      'http.route': req.route?.path,
      'request.id': req.headers['x-request-id']
    });
  }
  next();
});
```

### Database Query Spans

```python
with tracer.start_as_current_span("db.query") as span:
    span.set_attributes({
        "db.system": "postgresql",
        "db.operation": "SELECT",
        "db.collection.name": "users",
        # Never include literal values
        "db.statement": "SELECT * FROM users WHERE status = ?"
    })
```

### Custom Metrics

```go
// Counter for request processing
requestCounter := meter.Int64Counter(
    "http_requests_total",
    metric.WithDescription("Total HTTP requests"),
    metric.WithUnit("1"),
)

requestCounter.Add(ctx, 1, 
    metric.WithAttributes(
        attribute.String("method", r.Method),
        attribute.String("route", route),
        attribute.Int("status_code", statusCode),
    ),
)
