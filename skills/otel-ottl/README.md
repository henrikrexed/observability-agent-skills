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

# OpenTelemetry Transformation Language (OTTL) Skill

Complete reference for OTTL - the powerful transformation language built into the OpenTelemetry Collector. Transform, filter, and enrich telemetry data without changing application code.

## What OTTL Enables

### Data Transformation

- **Enrich** telemetry with metadata
- **Normalize** attribute names and values
- **Extract** structured data from logs
- **Convert** between data types

### Security & Compliance  

- **Redact** PII and sensitive data
- **Hash** user identifiers for correlation
- **Mask** patterns in log messages
- **Remove** high-cardinality attributes

### Performance Optimization

- **Control** attribute cardinality
- **Sample** data intelligently
- **Route** data to appropriate backends
- **Batch** related operations

## Quick Examples

### Basic Transformations

```yaml
# Set static attributes
set(resource.attributes["deployment.environment"], "production")

# Copy between fields
set(span.attributes["region"], resource.attributes["cloud.region"])

# Conditional setting
set(span.status.code, STATUS_CODE_ERROR) where span.attributes["http.status_code"] >= 500
```

### PII Protection

```yaml
# Redact authorization headers
set(span.attributes["http.request.header.authorization"], "REDACTED") where span.attributes["http.request.header.authorization"] != nil

# Hash user emails for correlation
set(span.attributes["user.email"], SHA256(span.attributes["user.email"])) where span.attributes["user.email"] != nil

# Mask credit cards in logs
replace_pattern(log.body["string"], "\\b(\\d{4})[\\d\\s-]{8,12}(\\d{4})\\b", "$1-****-****-$2")
```

### Kubernetes Enrichment

```yaml
# Set environment from namespace pattern
set(resource.attributes["deployment.environment"], "production") where IsMatch(resource.attributes["k8s.namespace.name"], "^prod-")

# Extract service tier from name
set(resource.attributes["service.tier"], "frontend") where IsMatch(resource.attributes["service.name"], "^(web|ui|frontend)")
```

## Key Benefits

### 🔒 Security First

Built-in patterns for PII redaction, sensitive data masking, and compliance requirements.

### 🚀 Production Ready

Tested patterns for high-scale environments with performance optimizations.

### 🎯 Framework Agnostic

Works with any telemetry data - traces, metrics, logs from any source.

### 🛠️ Complete Reference

Every OTTL function documented with practical examples and common patterns.

## Use Cases

- **Data Privacy**: Remove PII before telemetry reaches backends
- **Cost Control**: Reduce data volume through intelligent filtering
- **Service Discovery**: Enrich telemetry with infrastructure metadata  
- **Error Analysis**: Extract and categorize error patterns
- **Performance**: Optimize telemetry pipeline efficiency
