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

# Skills Overview

The OpenTelemetry Observability Agent Skills package includes 5 specialized skills that cover all aspects of OpenTelemetry implementation.

## Available Skills

<div class="grid cards" markdown>

- :fontawesome-solid-code:{ .lg .middle } **[Instrumentation](instrumentation.md)**

    ---

    SDK setup, spans, metrics, logs, and comprehensive PII protection patterns

    **Covers:** Auto-instrumentation, custom spans, metrics, error handling, security

- :fontawesome-solid-gear:{ .lg .middle } **[Collector](collector.md)**

    ---

    Pipeline configuration, processors, deployment, and production optimization

    **Covers:** Configuration, processors, deployment, performance, monitoring

- :fontawesome-solid-filter:{ .lg .middle } **[OTTL](ottl.md)**

    ---

    Complete OpenTelemetry Transformation Language reference and patterns

    **Covers:** Data transformation, filtering, enrichment, security, performance

- :fontawesome-solid-tags:{ .lg .middle } **[Semantic Conventions](semantic-conventions.md)**

    ---

    Attribute standards, naming patterns, and consistency guidelines

    **Covers:** Attributes, naming, versioning, migration, compliance

- :fontawesome-solid-chart-line:{ .lg .middle } **[Dynatrace](dynatrace.md)**

    ---

    DQL queries, dashboard creation, SLOs, workflows, and automation

    **Covers:** DQL, dashboards, SLOs, workflows, dtctl CLI, automation

</div>

## How Skills Work

Each skill provides:

### 📋 Rule-Based Guidance

Structured rules covering specific aspects of OpenTelemetry implementation, with clear examples and anti-patterns.

### 🔒 Security-First Approach

Every skill includes security considerations, PII protection patterns, and compliance guidelines.

### 🚀 Production-Ready Patterns

All patterns are tested in production environments and optimized for scale and reliability.

### 🎯 Framework-Specific Guidance

Tailored advice for popular frameworks and languages, with technology-specific best practices.

## Skill Integration

### Natural Language Prompts

Skills work best with natural language requests:

```
"Add OpenTelemetry tracing to my Express.js API with proper error handling and PII protection"

"Create an OpenTelemetry collector config for Kubernetes that exports to Dynatrace"

"Write OTTL rules to redact sensitive data from HTTP headers"
```

### AI Assistant Compatibility

Skills are designed to work with any AI assistant that supports:

- **Context injection** for background knowledge
- **Rule-based guidance** for structured advice
- **Example patterns** for code generation
- **Best practices** for quality assurance

### Technology Coverage

| Technology | Instrumentation | Collector | OTTL | Semantic Conventions | Dynatrace |
|------------|:---------------:|:---------:|:----:|:-------------------:|:---------:|
| **Node.js** | ✅ | ✅ | ✅ | ✅ | ✅ |
| **Python** | ✅ | ✅ | ✅ | ✅ | ✅ |
| **Go** | ✅ | ✅ | ✅ | ✅ | ✅ |
| **Java** | ✅ | ✅ | ✅ | ✅ | ✅ |
| **.NET** | ✅ | ✅ | ✅ | ✅ | ✅ |
| **Kubernetes** | ✅ | ✅ | ✅ | ✅ | ✅ |
| **Docker** | ✅ | ✅ | ✅ | ✅ | ✅ |

## Using Skills Effectively

### 1. Be Specific

Instead of "add observability," request specific implementations:

```
"Add OpenTelemetry instrumentation to this Express.js endpoint with span attributes following semantic conventions and PII protection for user data"
```

### 2. Include Context

Provide information about your environment:

```
"For a Python Django application deployed on Kubernetes with Dynatrace backend..."
```

### 3. Reference Skills Directly

Many assistants support skill references:

```
"@otel-instrumentation help me add tracing"
"Using the otel-collector skill, generate a config for..."
```

### 4. Security First

Always include security considerations:

```
"Ensure PII protection and follow security best practices"
```

## Skill Combinations

Skills work together for comprehensive solutions:

### Full Stack Implementation

```
1. Use otel-instrumentation for application tracing
2. Use otel-collector for data pipeline
3. Use otel-ottl for data transformation
4. Use otel-dynatrace for monitoring setup
```

### Security-Focused Implementation

```
1. otel-instrumentation: Secure SDK setup
2. otel-ottl: PII redaction rules
3. otel-semantic-conventions: Safe attribute patterns
```

### Performance-Optimized Implementation

```
1. otel-collector: Optimized pipeline config
2. otel-ottl: Efficient transformations
3. otel-dynatrace: Performance monitoring
```

## Next Steps

1. 📖 **Read individual skill documentation** for detailed guidance
2. 🧪 **Try the [examples](../examples/)** to see skills in action
3. 🎯 **Choose your [AI assistant setup](../getting-started/)** for optimal integration
4. 🚀 **Start building** with natural language prompts

Each skill page includes comprehensive documentation with examples, best practices, and integration guidance specific to that area of OpenTelemetry.
