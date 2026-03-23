# Production Patterns

## High-Availability Collector
```yaml
# Gateway pattern: agent (DaemonSet) + gateway (Deployment)
```

## PII Redaction Pipeline
```yaml
processors:
  transform/pii:
    log_statements:
      - context: log
        statements:
          - replace_pattern(body, "\\b[\\w.-]+@[\\w.-]+\\.\\w+\\b", "[REDACTED]")
```

## Tail Sampling
```yaml
processors:
  tail_sampling:
    policies:
      - name: errors-always
        type: ottl_condition
        ottl_condition:
          span:
            - attributes["http.status_code"] >= 500
```
