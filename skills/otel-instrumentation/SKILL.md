---
name: 'otel-instrumentation'
description: OpenTelemetry SDK instrumentation patterns, spans, metrics, logs, and PII protection. Triggers on instrumentation, tracing, metrics, observability setup.
license: Apache-2.0
metadata:
  author: henrikrexed
  version: '1.0.0'
  workflow_type: 'advisory'
---

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

# OpenTelemetry Instrumentation

Production-ready patterns for instrumenting applications with OpenTelemetry SDKs. Covers traces, metrics, logs, and comprehensive security practices.

## Rules

| Rule | Description |
|------|-------------|
| [telemetry](./rules/telemetry.md) | Signal types, correlation, and data relationships |
| [resources](./rules/resources.md) | Resource attributes, service identity, and metadata |
| [spans](./rules/spans.md) | Span naming, kinds, status, and lifecycle |
| [metrics](./rules/metrics.md) | Instrument types, naming, and cardinality control |
| [logs](./rules/logs.md) | Structured logging, severity, and correlation |
| [sensitive-data](./rules/sensitive-data.md) | PII prevention, redaction, and compliance |
| [nodejs](./rules/sdks/nodejs.md) | Node.js specific instrumentation patterns |
| [python](./rules/sdks/python.md) | Python specific instrumentation patterns |
| [go](./rules/sdks/go.md) | Go specific instrumentation patterns |
| [java](./rules/sdks/java.md) | Java specific instrumentation patterns |
| [dotnet](./rules/sdks/dotnet.md) | .NET specific instrumentation patterns |

## Key Principles

### Security First

- Never instrument credentials, API keys, or PII
- Sanitize URLs to remove sensitive query parameters
- Use hashing for user identifiers when correlation is needed
- Implement defensive programming practices

### Production Readiness

- Configure proper error handling and fallbacks
- Set appropriate resource attributes for service discovery
- Use semantic conventions for consistent naming
- Implement cardinality controls to prevent metric explosions

### Performance Awareness

- Use sampling to control data volume
- Batch operations when possible  
- Avoid synchronous exports in critical paths
- Monitor instrumentation overhead

### Correlation Excellence

- Propagate trace context across service boundaries
- Use consistent resource attributes for service grouping
- Link related spans with parent-child relationships
- Correlate logs with traces using trace IDs

## Common Patterns

### Basic Setup

Always start with proper SDK initialization and resource detection.

### Error Handling  

Set span status and record exceptions appropriately.

### Async Operations

Handle context propagation correctly in async workflows.

### HTTP Instrumentation

Capture request/response metadata while protecting sensitive data.

### Database Queries

Record operation details without exposing literal values.

### Custom Metrics

Choose appropriate instrument types and manage cardinality.
