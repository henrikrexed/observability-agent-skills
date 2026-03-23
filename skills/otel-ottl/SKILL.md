---
name: 'otel-ottl'
description: OpenTelemetry Transformation Language (OTTL) complete reference and patterns. Triggers on OTTL, transformation, filtering, data processing, telemetry manipulation.
license: MIT
metadata:
  author: henrikrexed
  version: '1.0.0'
  workflow_type: 'advisory'
---

# OpenTelemetry Transformation Language (OTTL)

Complete reference and production patterns for the OpenTelemetry Transformation Language. OTTL enables powerful data transformation, filtering, and enrichment within the OpenTelemetry Collector.

## OTTL Fundamentals

### Where OTTL is Used
- **transform processor**: Modify, enrich, redact telemetry data
- **filter processor**: Drop telemetry based on conditions
- **attributes processor**: Manage resource and record attributes
- **tailsampling processor**: Sample traces using OTTL conditions
- **routing connector**: Route data to different pipelines
- **count connector**: Generate metrics from spans/logs

### Context Types

| Context | Description | Use With |
|---------|-------------|----------|
| `resource` | Resource-level attributes | All signal types |
| `scope` | Instrumentation scope | All signal types |
| `span` | Span data | Traces only |
| `spanevent` | Span events | Traces only |
| `metric` | Metric metadata | Metrics only |
| `datapoint` | Metric data points | Metrics only |
| `log` | Log records | Logs only |

### Path Expressions
Navigate telemetry data using dot notation:

```ottl
span.name
span.attributes["http.method"]
resource.attributes["service.name"]
log.body["message"]
metric.name
```

## Core Functions

### Editor Functions (lowercase - modify data)

| Function | Purpose | Example |
|----------|---------|---------|
| `set` | Set a field value | `set(span.attributes["env"], "prod")` |
| `delete_key` | Remove map key | `delete_key(span.attributes, "internal.key")` |
| `delete_matching_keys` | Remove keys by pattern | `delete_matching_keys(span.attributes, "^temp\\.")` |
| `replace_pattern` | Replace text via regex | `replace_pattern(log.body["string"], "\\d{4}", "****")` |
| `merge_maps` | Merge two maps | `merge_maps(span.attributes, resource.attributes, "upsert")` |
| `flatten` | Flatten nested structures | `flatten(span.attributes, "nested.", 2)` |
| `limit` | Limit map size | `limit(span.attributes, 50, ["trace.id"])` |
| `truncate_all` | Truncate string values | `truncate_all(span.attributes, 1024)` |

### Converter Functions (uppercase - return values)

#### Type Checking
```ottl
IsString(span.attributes["user.id"])
IsInt(span.attributes["http.status_code"])
IsMap(span.attributes["metadata"])
IsMatch(metric.name, "^k8s\\.")
```

#### Type Conversion
```ottl
String(span.attributes["http.status_code"])
Int(span.attributes["duration_ms"])
Double(span.attributes["cpu_usage"])
Bool(span.attributes["is_error"])
```

#### String Operations
```ottl
Concat([resource.attributes["service.name"], span.name], ".")
Substring(log.body["string"], 0, 100)
ToLowerCase(span.attributes["http.method"])
Split(span.attributes["tags"], ",")
Trim(log.body["message"])
```

#### Parsing
```ottl
ParseJSON(log.body["json_data"])
ExtractPatterns(log.body["string"], "user=(?P<user>\\w+)")
ParseKeyValue(log.body["kv_data"], "=", " ")
```

#### Hashing
```ottl
SHA256(span.attributes["user.email"])
MD5(span.attributes["session.id"])
```

## Production Patterns

### PII Redaction Pipeline
```yaml
processors:
  transform/redact-pii:
    error_mode: ignore
    trace_statements:
      - context: span
        statements:
          # Redact authorization headers
          - set(span.attributes["http.request.header.authorization"], "REDACTED") where span.attributes["http.request.header.authorization"] != nil
          # Hash user identifiers
          - set(span.attributes["user.email"], SHA256(span.attributes["user.email"])) where span.attributes["user.email"] != nil
          # Remove sensitive patterns
          - delete_matching_keys(span.attributes, "(?i).*(password|secret|token|key).*")
    log_statements:
      - context: log
        statements:
          # Mask credit card numbers
          - replace_pattern(log.body["string"], "\\b(\\d{4})[\\d\\s-]{8,12}(\\d{4})\\b", "$1-****-****-$2")
          # Remove social security numbers
          - replace_pattern(log.body["string"], "\\b\\d{3}-\\d{2}-\\d{4}\\b", "***-**-****")
```

### Kubernetes Enrichment
```yaml
processors:
  transform/k8s-enrich:
    error_mode: ignore
    trace_statements:
      - context: resource
        statements:
          # Set environment from namespace
          - set(resource.attributes["deployment.environment"], "production") where IsMatch(resource.attributes["k8s.namespace.name"], "^prod-")
          - set(resource.attributes["deployment.environment"], "staging") where IsMatch(resource.attributes["k8s.namespace.name"], "^staging-")
          # Add cluster context
          - set(resource.attributes["k8s.cluster.name"], "prod-us-west-2")
          # Extract service tier
          - set(resource.attributes["service.tier"], "frontend") where IsMatch(resource.attributes["service.name"], "^(web|ui|frontend)")
```

### HTTP Enhancement
```yaml
processors:
  transform/http-enhance:
    error_mode: ignore
    trace_statements:
      - context: span
        statements:
          # Normalize HTTP methods
          - set(span.attributes["http.method"], ToUpperCase(span.attributes["http.method"]))
          # Set span status from HTTP status
          - set(span.status.code, STATUS_CODE_ERROR) where span.attributes["http.status_code"] >= 400
          # Create route patterns
          - set(span.attributes["http.route"], replace_pattern(span.attributes["http.target"], "/\\d+", "/{id}"))
          # Sanitize URLs
          - set(span.attributes["http.url"], Concat([Split(span.attributes["http.url"], "?")[0]], ""))
```

### Cardinality Control
```yaml
processors:
  transform/cardinality-control:
    error_mode: ignore
    trace_statements:
      - context: span
        statements:
          # Limit attribute count
          - limit(span.attributes, 20, ["service.name", "http.method", "http.status_code"])
          # Truncate long values
          - truncate_all(span.attributes, 256)
          # Remove high-cardinality user data
          - delete_matching_keys(span.attributes, "^user\\.(id|email|session)")
    metric_statements:
      - context: datapoint
        statements:
          # Control metric label cardinality
          - limit(datapoint.attributes, 10, ["service.name", "endpoint"])
```

### Conditional Processing
```yaml
processors:
  transform/conditional:
    error_mode: ignore
    trace_statements:
      - context: span
        statements:
          # Different processing for different services
          - set(span.attributes["processing.level"], "strict") where resource.attributes["service.name"] == "payment-service"
          - delete_matching_keys(span.attributes, ".*") where resource.attributes["service.name"] == "payment-service" and not IsMatch(attributes.keys(), "^(service|http|db)\\.")
          # Error enrichment
          - set(span.attributes["error.category"], "timeout") where IsMatch(span.status.message, "(?i)timeout")
          - set(span.attributes["error.category"], "auth") where span.attributes["http.status_code"] == 401
```

## Advanced Techniques

### Temp Map Pattern
For complex operations, use temporary storage:

```yaml
trace_statements:
  - context: span
    statements:
      # Parse URL into temp map
      - set(cache["url"], ParseJSON(Concat(["{\"full\":\"", span.attributes["http.url"], "\"}"], "")))
      # Extract components
      - set(span.attributes["http.host"], Split(Split(cache["url"]["full"], "/")[2], ":")[0])
      - set(span.attributes["http.path"], Split(cache["url"]["full"], "?")[0])
      # Clean up
      - delete_key(cache, "url")
```

### Multi-Step Transformations
```yaml
log_statements:
  - context: log
    statements:
      # Step 1: Extract structured data
      - set(cache["parsed"], ParseJSON(log.body["string"])) where IsString(log.body["string"])
      # Step 2: Validate and extract fields
      - set(log.attributes["user.id"], SHA256(cache["parsed"]["userId"])) where cache["parsed"]["userId"] != nil
      - set(log.attributes["action"], cache["parsed"]["action"]) where cache["parsed"]["action"] != nil
      # Step 3: Clean up temp data
      - delete_key(cache, "parsed")
```

### Error Handling Patterns
```yaml
trace_statements:
  - context: span
    statements:
      # Safe parsing with nil checks
      - set(span.attributes["safe_data"], ParseJSON(span.attributes["json_data"])) where IsString(span.attributes["json_data"]) and span.attributes["json_data"] != nil
      # Defensive attribute setting
      - set(span.attributes["user.tier"], span.attributes["user.metadata.tier"]) where IsMap(span.attributes["user.metadata"]) and span.attributes["user.metadata.tier"] != nil
```

## Filtering Patterns

### Drop Sensitive Data
```yaml
processors:
  filter/drop-sensitive:
    error_mode: ignore
    traces:
      span:
        # Drop spans with PII
        - 'IsMatch(span.name, "(?i).*(auth|login|password).*")'
        # Drop health checks
        - 'span.attributes["http.target"] == "/health"'
        # Drop internal spans
        - 'span.attributes["internal"] == true'
    logs:
      log_record:
        # Drop logs with credentials
        - 'IsMatch(log.body["string"], "(?i)(password|token|key)\\s*[:=]")'
        # Drop verbose debug logs in production
        - 'log.severity_text == "DEBUG" and resource.attributes["deployment.environment"] == "production"'
```

### Sampling Decisions
```yaml
processors:
  tailsampling:
    policies:
      - name: errors
        type: string_attribute
        string_attribute:
          key: span.status.code
          values: [STATUS_CODE_ERROR]
      - name: slow_requests
        type: ottl_condition
        ottl_condition:
          error_mode: ignore
          span:
            - 'span.kind == SPAN_KIND_SERVER and span.duration > duration("1s")'
```

## Real-World Examples

### Complete PCI Compliance Pipeline
```yaml
processors:
  # Stage 1: Remove forbidden data
  transform/pci-forbidden:
    error_mode: ignore
    trace_statements:
      - context: span
        statements:
          - delete_matching_keys(span.attributes, "(?i).*(card|pan|cvv|pin).*")
    log_statements:
      - context: log
        statements:
          - replace_pattern(log.body["string"], "\\b(?:4[0-9]{12}(?:[0-9]{3})?|5[1-5][0-9]{14})\\b", "****-****-****-****")

  # Stage 2: Hash remaining user data
  transform/pci-hash:
    error_mode: ignore
    trace_statements:
      - context: span
        statements:
          - set(span.attributes["customer.id"], SHA256(span.attributes["customer.id"])) where span.attributes["customer.id"] != nil
```

### Multi-Tenant Data Isolation
```yaml
processors:
  transform/tenant-isolation:
    error_mode: ignore
    trace_statements:
      - context: span
        statements:
          # Tag spans with tenant
          - set(span.attributes["tenant.id"], resource.attributes["k8s.namespace.name"])
          # Redact cross-tenant data
          - delete_key(span.attributes, "external.tenant.data") where span.attributes["tenant.id"] != resource.attributes["k8s.namespace.name"]
          # Add tenant-specific routing tags
          - set(span.attributes["routing.tenant"], Concat(["tenant-", span.attributes["tenant.id"]], ""))
```

## Testing and Validation

### OTTL Expression Testing
Use the OTTL playground or collector debug mode:

```bash
# Test OTTL expressions
otelcol --config=test-config.yaml --set=processors.transform.error_mode=propagate
```

### Validation Patterns
```yaml
processors:
  transform/validation:
    error_mode: ignore
    trace_statements:
      - context: span
        statements:
          # Validate required attributes exist
          - set(span.attributes["validation.service_name"], "missing") where resource.attributes["service.name"] == nil
          - set(span.attributes["validation.trace_id"], "invalid") where not IsMatch(span.trace_id, "^[0-9a-f]{32}$")
```

## Performance Considerations

### Optimize for Scale
```yaml
processors:
  transform/optimized:
    error_mode: ignore
    trace_statements:
      - context: span
        statements:
          # Use simple checks before expensive operations
          - set(span.attributes["processed"], "true") where span.attributes["processed"] == nil and IsString(span.name)
          # Batch related operations
          - set(span.attributes["normalized.method"], ToUpperCase(span.attributes["http.method"])) where span.attributes["http.method"] != nil
          - set(span.attributes["normalized.status"], String(span.attributes["http.status_code"])) where span.attributes["http.status_code"] != nil
```

### Avoid Performance Anti-Patterns
```yaml
# ❌ Don't do expensive operations on every span
- set(span.attributes["expensive"], SHA256(Concat([span.trace_id, span.span_id, String(span.start_time_unix_nano)], "")))

# ✅ Use simpler alternatives when possible
- set(span.attributes["span.kind.name"], "server") where span.kind == SPAN_KIND_SERVER
```