---
name: 'otel-dynatrace'
description: Dynatrace integration patterns - DQL queries, dashboard creation, SLOs, workflows, and dtctl CLI. Triggers on Dynatrace, DQL, dashboard, SLO, workflow, dtctl.
license: MIT
metadata:
  author: henrikrexed
  version: '1.0.0'
  workflow_type: 'advisory'
---

# Dynatrace OpenTelemetry Integration

Production patterns for Dynatrace integration with OpenTelemetry, including DQL queries, dashboard creation, SLO management, and automation workflows.

## Rules

| Rule | Description |
|------|-------------|
| [dtctl](./rules/dtctl.md) | dtctl CLI reference and dashboard-as-code patterns |
| [dql](./rules/dql.md) | DQL query patterns and best practices |
| [dashboards](./rules/dashboards.md) | Dashboard creation, layout, and visualizations |
| [slos](./rules/slos.md) | SLO definition, evaluation, and alerting |
| [workflows](./rules/workflows.md) | Workflow automation and notifications |

## Key Principles

### Data-Driven Observability
- Use DQL to extract insights from OpenTelemetry data
- Create meaningful dashboards that drive decisions
- Define SLOs based on business requirements
- Automate responses to observability events

### Dashboard-as-Code
- Version control dashboard definitions
- Use dtctl for programmatic dashboard management  
- Implement consistent styling and layouts
- Enable team collaboration through code

### Proactive Monitoring
- Set up predictive alerts before issues impact users
- Use workflows for automated incident response
- Create SLOs that align with business objectives
- Build dashboards that surface actionable insights

### OpenTelemetry Integration
- Leverage semantic conventions in DQL queries
- Create dashboards optimized for OTel data structure
- Use trace context for cross-service analysis
- Build SLOs from standardized telemetry metrics

## Common Patterns

### Service Health Dashboards
Comprehensive service monitoring with OTel data.

### Error Rate Analysis
DQL queries for identifying and categorizing errors.

### Performance Trending
Track latency and throughput over time.

### Infrastructure Correlation  
Link application performance to infrastructure health.

### Business KPI Tracking
Monitor business metrics alongside technical metrics.