# OpenTelemetry Observability Agent Skills

Universal AgentSkills package providing comprehensive OpenTelemetry guidance for AI coding assistants. Supports Claude Code, GitHub Copilot, Cursor, Windsurf, and other AI-powered development tools.

## 🎯 What This Is

A collection of specialized skills that help AI assistants write better OpenTelemetry code:

- **Production-ready patterns** from real-world implementations
- **Security-first approach** with built-in PII protection
- **Kubernetes-native** configurations and best practices  
- **Dynatrace integration** patterns not found elsewhere
- **OTTL mastery** for data transformation and enrichment

## 🚀 Quick Start

```bash
npx skills add henrikrexed/observability-agent-skills
```

📖 **[Complete Documentation](https://henrikrexed.github.io/observability-agent-skills/)**

## 🔧 Getting Started

Choose your AI assistant for specific setup instructions:

| Assistant | Setup Guide | Best For |
|-----------|-------------|----------|
| [Claude Code](https://henrikrexed.github.io/observability-agent-skills/getting-started/claude-code/) | CLAUDE.md + slash commands | Dedicated Claude workspaces |
| [Cursor](https://henrikrexed.github.io/observability-agent-skills/getting-started/cursor/) | .cursorrules configuration | VS Code + Cursor users |
| [GitHub Copilot](https://henrikrexed.github.io/observability-agent-skills/getting-started/github-copilot/) | Enhanced context files | GitHub-integrated workflows |
| [Windsurf](https://henrikrexed.github.io/observability-agent-skills/getting-started/windsurf/) | .windsurfrules optimization | Windsurf AI environment |
| [Generic](https://henrikrexed.github.io/observability-agent-skills/getting-started/generic/) | Universal MCP/AgentSkills | Other AI assistants |

## 📋 Skills Overview

| Skill | Description | When to Use |
|-------|-------------|-------------|
| **[otel-instrumentation](./skills/otel-instrumentation/)** | SDK setup, spans, metrics, logs, PII protection | Adding observability to applications |
| **[otel-collector](./skills/otel-collector/)** | Pipeline config, processors, deployment | Setting up data collection |
| **[otel-ottl](./skills/otel-ottl/)** | Complete OTTL reference and patterns | Transforming telemetry data |
| **[otel-semantic-conventions](./skills/otel-semantic-conventions/)** | Attribute standards, naming patterns | Ensuring consistent telemetry |
| **[otel-dynatrace](./skills/otel-dynatrace/)** | DQL, dashboards, SLOs, dtctl CLI | Dynatrace-specific workflows |

## 💬 Usage Examples

Tell your AI assistant what you want in natural language:

### Instrumentation
```
"Add OpenTelemetry tracing to my Express.js API with proper error handling"

"Set up Python observability with sensitive data redaction for a Django app"

"Configure OpenTelemetry metrics for a Go microservice in Kubernetes"
```

### Collector Configuration  
```
"Create an OpenTelemetry collector config for Kubernetes with Dynatrace export"

"Write OTTL rules to redact PII from HTTP headers and log messages"

"Set up tail-based sampling for high-volume trace data"
```

### Dynatrace Integration
```
"Generate a DQL query to analyze API error rates by service and region"

"Create a Dynatrace dashboard for monitoring OpenTelemetry collector health"

"Write a workflow to alert on SLO violations with actionable context"
```

## 🔧 AI Assistant Compatibility

### Claude Code
```bash
# Skills are automatically available in your Claude Code workspace
claude instrumentation help  # Get instrumentation guidance
```

### Cursor / Windsurf
```bash
# Reference skills in your prompts
@observability Add OTEL tracing to this service
```

### GitHub Copilot
```javascript
// @observability-agent-skills: setup nodejs instrumentation
// Copilot will use the skills to generate appropriate code
```

### VS Code Extensions
Skills integrate with VS Code through the AgentSkills protocol, providing contextual assistance in:
- Code completions
- Error diagnostics  
- Refactoring suggestions

## 🆚 How This Differs from dash0hq/agent-skills

| Feature | This Package | dash0hq/agent-skills |
|---------|-------------|---------------------|
| **Focus** | OpenTelemetry + Dynatrace | General observability |
| **Coverage** | Complete OTTL reference | Basic patterns |
| **Security** | Comprehensive PII protection | Limited |
| **Kubernetes** | Production patterns | Basic examples |
| **Backend** | Dynatrace-native | Platform agnostic |

**Use together:** This package complements `dash0hq/agent-skills` — install both for comprehensive coverage.

## 📚 Learning Resources

Created by Henrik Rexed, maintainer of the **[IsItObservable](https://www.youtube.com/@IsItObservable)** YouTube channel:

- 🎥 [OpenTelemetry Fundamentals](https://youtube.com/playlist?list=PLqt2rd0eew1YFx9m8dBFSiGYSBcPq8ggD)
- 🎥 [Kubernetes Observability](https://youtube.com/playlist?list=PLqt2rd0eew1aFqHWpds-hR9jrjLSvBDvz)  
- 🎥 [Dynatrace + OpenTelemetry](https://youtube.com/playlist?list=PLqt2rd0eew1bmH5J5AXS3B8pXBvDfAM7W)

## 🛠️ Development

Built with the AgentSkills specification:

```bash
git clone https://github.com/henrikrexed/observability-agent-skills.git
cd observability-agent-skills

# Test skills locally
npx skills test ./skills/otel-instrumentation/

# Validate format
npx skills validate ./skills/
```

## 📄 License

MIT License - See [LICENSE](./LICENSE) for details.

---

**Built for developers, by developers** • Follow [@henrikrexed](https://twitter.com/henrikrexed) for updates