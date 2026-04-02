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

# OpenTelemetry Observability Agent Skills

Universal AgentSkills package providing comprehensive OpenTelemetry guidance for AI coding assistants. Works with Claude Code, GitHub Copilot, Cursor, Windsurf, and other AI-powered development tools.

## 🚀 Quick Start

Install the skills package:

```bash
npx skills add henrikrexed/observability-agent-skills
```

## 🎯 What This Provides

A comprehensive collection of specialized skills that help AI assistants write better OpenTelemetry code:

<div class="grid cards" markdown>

- :fontawesome-solid-code:{ .lg .middle } **Production-Ready Patterns**

    ---

    Battle-tested patterns from real-world implementations running at scale

- :fontawesome-solid-shield:{ .lg .middle } **Security First**

    ---

    Built-in PII protection, sensitive data redaction, and compliance patterns

- :fontawesome-solid-dharmachakra:{ .lg .middle } **Kubernetes Native**

    ---

    Container-optimized configurations and deployment best practices

- :fontawesome-solid-chart-line:{ .lg .middle } **Dynatrace Integration**

    ---

    Advanced DQL queries, dashboards, SLOs, and automation workflows

</div>

## 📋 Available Skills

| Skill | Description | Best For |
|-------|-------------|----------|
| **[otel-instrumentation](skills/instrumentation.md)** | SDK setup, spans, metrics, logs, PII protection | Adding observability to applications |
| **[otel-collector](skills/collector.md)** | Pipeline config, processors, deployment | Setting up data collection |
| **[otel-ottl](skills/ottl.md)** | Complete OTTL reference and patterns | Transforming telemetry data |
| **[otel-semantic-conventions](skills/semantic-conventions.md)** | Attribute standards, naming patterns | Ensuring consistent telemetry |
| **[otel-dynatrace](skills/dynatrace.md)** | DQL, dashboards, SLOs, dtctl CLI | Dynatrace-specific workflows |

## 💬 Natural Language Examples

Tell your AI assistant what you want in plain English:

!!! example "Instrumentation Requests"

    - "Add OpenTelemetry tracing to my Express.js API with proper error handling"
    - "Set up Python observability with sensitive data redaction for a Django app"  
    - "Configure OpenTelemetry metrics for a Go microservice in Kubernetes"

!!! example "Collector Configuration"

    - "Create an OpenTelemetry collector config for Kubernetes with Dynatrace export"
    - "Write OTTL rules to redact PII from HTTP headers and log messages"
    - "Set up tail-based sampling for high-volume trace data"

!!! example "Dynatrace Integration"

    - "Generate a DQL query to analyze API error rates by service and region"
    - "Create a Dynatrace dashboard for monitoring OpenTelemetry collector health"
    - "Write a workflow to alert on SLO violations with actionable context"

## 🔧 AI Assistant Compatibility

### :simple-anthropic: Claude Code

Skills are automatically available in your workspace. Use with slash commands:

```bash
claude instrumentation help  # Get instrumentation guidance
claude collector config     # Generate collector configuration
```

### :simple-cursor: Cursor

Reference skills in your prompts with `@`:

```bash
@observability Add OTEL tracing to this service
@observability Create collector config for Kubernetes
```

### :simple-github: GitHub Copilot

Skills integrate through comments and chat:

```javascript
// @observability-agent-skills: setup nodejs instrumentation
// Copilot will use skills to generate appropriate code
```

### :material-microsoft-visual-studio-code: VS Code Extensions

Skills work with any MCP/AgentSkills-compatible extension, providing:

- Contextual code completions
- Error diagnostics and fixes
- Intelligent refactoring suggestions

## 📖 Getting Started

Choose your AI assistant to get specific setup instructions:

<div class="grid cards" markdown>

- :simple-anthropic:{ .lg .middle } [**Claude Code**](getting-started/claude-code.md)

    ---

    Setup for Claude Code with CLAUDE.md and slash commands

- :simple-cursor:{ .lg .middle } [**Cursor**](getting-started/cursor.md)

    ---

    Configuration with .cursorrules and IDE integration

- :simple-github:{ .lg .middle } [**GitHub Copilot**](getting-started/github-copilot.md)

    ---

    Setup with copilot-instructions.md for enhanced context

- :material-microsoft-visual-studio-code:{ .lg .middle } [**Windsurf**](getting-started/windsurf.md)

    ---

    Configuration with .windsurfrules for optimal integration

- :material-robot:{ .lg .middle } [**Generic Setup**](getting-started/generic.md)

    ---

    Universal setup for any MCP/AgentSkills-compatible assistant

</div>

## 📚 Learning Resources

Created by Henrik Rexed, maintainer of the **[IsItObservable](https://www.youtube.com/@IsItObservable)** YouTube channel:

- 🎥 [OpenTelemetry Fundamentals](https://youtube.com/playlist?list=PLqt2rd0eew1YFx9m8dBFSiGYSBcPq8ggD)
- 🎥 [Kubernetes Observability](https://youtube.com/playlist?list=PLqt2rd0eew1aFqHWpds-hR9jrjLSvBDvz)  
- 🎥 [Dynatrace + OpenTelemetry](https://youtube.com/playlist?list=PLqt2rd0eew1bmH5J5AXS3B8pXBvDfAM7W)

## 🤝 Contributing

This is an open-source project. Contributions are welcome!

- 🐛 [Report Issues](https://github.com/henrikrexed/observability-agent-skills/issues)
- 🔧 [Submit Pull Requests](https://github.com/henrikrexed/observability-agent-skills/pulls)
- 💡 [Suggest Features](https://github.com/henrikrexed/observability-agent-skills/discussions)

---

**Ready to get started?** Choose your AI assistant from the [Getting Started](getting-started/) section and begin building better observability! 🚀
