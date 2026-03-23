# Dynatrace OpenTelemetry Integration Skill

Comprehensive patterns for integrating OpenTelemetry with Dynatrace, including advanced DQL queries, dashboard automation, SLO management, and workflow orchestration.

## What You'll Learn

- **DQL Mastery**: Query OpenTelemetry data effectively
- **Dashboard Automation**: Dashboard-as-code with dtctl
- **SLO Management**: Define and monitor service level objectives
- **Workflow Automation**: Automated incident response
- **Performance Analysis**: Advanced observability patterns

## Key Features

### 📊 Advanced DQL Patterns
Query OpenTelemetry traces, metrics, and logs with optimized DQL patterns.

```dql
// Service error rate analysis
fetch spans
| filter span.kind == "server" and isNotNull(resource.attributes["service.name"])
| summarize error_count = countIf(span.status == "ERROR"), 
           total_count = count(), 
           by: {resource.attributes["service.name"], bin(timestamp, 5m)}
| fieldsAdd error_rate = error_count / total_count * 100
```

### 🛠️ Dashboard as Code
Programmatic dashboard creation and management using dtctl.

```bash
# Create dashboard from definition
dtctl create dashboard --file=service-overview.json

# Apply dashboard updates
dtctl update dashboard --id=<dashboard-id> --file=updated-dashboard.json
```

### 🎯 SLO Automation  
Define SLOs that align with business requirements.

```json
{
  "name": "API Availability SLO",
  "target": 99.9,
  "timeframe": "30d",
  "sli": {
    "numerator": "fetch spans | filter span.kind == 'server' and span.status != 'ERROR' | count",
    "denominator": "fetch spans | filter span.kind == 'server' | count"
  }
}
```

### ⚡ Workflow Integration
Automated responses to observability events.

```yaml
workflows:
  - name: "High Error Rate Response"
    trigger:
      type: "slo_violation" 
      slo: "API Availability SLO"
    actions:
      - type: "slack_notification"
        channel: "#incidents"
      - type: "pagerduty_alert"
        service: "api-service"
```

## Integration Benefits

### 🔗 OpenTelemetry Native
Patterns specifically designed for OpenTelemetry semantic conventions and data structures.

### 🚀 Production Proven
Real-world patterns from high-scale production environments.

### 🎨 Dashboard Excellence
Professional dashboard layouts with consistent styling and meaningful visualizations.

### 🤖 Automation Ready
Everything designed for automation, CI/CD integration, and infrastructure as code.