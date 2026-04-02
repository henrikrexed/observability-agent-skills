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
