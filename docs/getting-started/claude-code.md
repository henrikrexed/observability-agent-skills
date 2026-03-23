# Claude Code Setup

Configure OpenTelemetry observability skills for Claude Code with dedicated workspace integration.

## Installation

1. **Install the skills package:**
   ```bash
   npx skills add henrikrexed/observability-agent-skills
   ```

2. **Create workspace configuration files** (choose the setup that fits your project):

## Complete Workspace Setup

### Create CLAUDE.md
Create `CLAUDE.md` in your project root with comprehensive observability context:

```markdown
# OpenTelemetry Project

This project uses OpenTelemetry for observability. Use the installed observability-agent-skills for guidance.

## Skills Available
- otel-instrumentation: SDK setup, spans, metrics, logs, PII protection
- otel-collector: Pipeline configuration and deployment  
- otel-ottl: Data transformation and filtering
- otel-semantic-conventions: Attribute standards
- otel-dynatrace: DQL queries, dashboards, SLOs

## Project Context
- Language: [Your language - Node.js, Python, Go, Java, .NET]
- Framework: [Your framework - Express, Django, Gin, Spring Boot, etc.]
- Deployment: [Your environment - Kubernetes, Docker, Cloud, etc.]
- Backend: [Your observability backend - Dynatrace, Jaeger, OTLP, etc.]

## Current Observability State
- [ ] Basic instrumentation
- [ ] Custom metrics
- [ ] Structured logging
- [ ] Error tracking
- [ ] Performance monitoring
- [ ] Security/PII protection

## Guidelines
1. Always include PII protection in instrumentation
2. Use semantic conventions for consistent naming
3. Implement proper error handling and span status
4. Consider cardinality when creating custom metrics
5. Test instrumentation in non-production first

## Useful Commands
- `claude otel help` - Show available OpenTelemetry patterns
- `claude instrument [service]` - Add instrumentation to service
- `claude collector config` - Generate collector configuration
- `claude ottl transform` - Create OTTL transformation rules
```

### Create .claude/settings.json
Create `.claude/settings.json` to configure Claude Code behavior:

```json
{
  "skills": {
    "observability-agent-skills": {
      "enabled": true,
      "autoSuggest": true,
      "contexts": [
        "instrumentation",
        "collector",
        "ottl",
        "semantic-conventions", 
        "dynatrace"
      ]
    }
  },
  "prompting": {
    "includeContext": true,
    "focusAreas": [
      "security",
      "performance", 
      "production-readiness"
    ]
  },
  "codeGeneration": {
    "includeComments": true,
    "includeErrorHandling": true,
    "followSemanticConventions": true
  }
}
```

### Create .claude/commands/
Add custom slash commands for quick access:

```bash
mkdir -p .claude/commands
```

#### .claude/commands/otel.md
```markdown
# OpenTelemetry Commands

## /otel instrument [language]
Add basic instrumentation to the current service.

Usage: `/otel instrument nodejs`

## /otel collector
Generate OpenTelemetry collector configuration.

## /otel security
Add PII protection and sensitive data redaction.

## /otel k8s
Generate Kubernetes deployment with observability.

## /otel dashboard
Create Dynatrace dashboard configuration.
```

## Testing Your Setup

### Basic Instrumentation Test
Ask Claude Code:

```
Add OpenTelemetry instrumentation to this Express.js service with proper error handling and PII protection
```

Expected response should include:
- SDK initialization with proper configuration
- Span creation with semantic conventions
- Error handling with span status
- PII protection patterns
- Resource attribute configuration

### Collector Configuration Test
Ask Claude Code:

```
Create an OpenTelemetry collector configuration for Kubernetes that exports to Dynatrace with OTTL transformations for PII redaction
```

Expected response should include:
- Complete collector YAML configuration
- Kubernetes deployment manifests
- OTTL transformation rules
- Dynatrace exporter configuration
- Security best practices

### Security Test
Ask Claude Code:

```
Review this instrumentation code for sensitive data exposure and suggest improvements
```

Then provide any instrumentation code. Claude should identify:
- Potential PII in attributes
- Sensitive data in URLs or headers
- Missing sanitization patterns
- Security best practices

## Claude Code Specific Features

### Slash Commands
Use these slash commands for quick access:

```bash
/skills list                           # Show available skills
/skills help otel-instrumentation     # Get help for specific skill
/claude otel instrument               # Add instrumentation
/claude collector config              # Generate collector config
/claude security review               # Security review
```

### Auto-Context
Claude Code automatically includes project context:

- Detects your programming language and framework
- Understands your current observability setup
- Suggests appropriate patterns based on your environment
- Maintains consistency across code suggestions

### Workspace Memory
Claude Code remembers:

- Your observability architecture decisions
- Previously implemented patterns
- Security and compliance requirements
- Performance optimization choices

## Project-Specific Configuration

### Node.js Project
Add to your CLAUDE.md:

```markdown
## Node.js Specific
- Framework: Express.js
- Package manager: npm/yarn/pnpm
- Runtime: Node.js 18+
- Deployment: Docker + Kubernetes

## Dependencies
- @opentelemetry/auto-instrumentations-node
- @opentelemetry/exporter-otlp-grpc
- @opentelemetry/semantic-conventions
```

### Python Project
Add to your CLAUDE.md:

```markdown
## Python Specific  
- Framework: Django/FastAPI/Flask
- Package manager: pip/poetry/conda
- Runtime: Python 3.9+
- WSGI: Gunicorn/uWSGI

## Dependencies
- opentelemetry-distro[otlp]
- opentelemetry-exporter-otlp-proto-grpc
- opentelemetry-instrumentation
```

## Advanced Configuration

### Custom Skill Priorities
In `.claude/settings.json`, prioritize specific skills:

```json
{
  "skills": {
    "priorities": [
      "otel-instrumentation",
      "otel-ottl",
      "otel-dynatrace"
    ],
    "contexts": {
      "backend": ["otel-instrumentation", "otel-collector"],
      "frontend": ["otel-instrumentation"],
      "infrastructure": ["otel-collector", "otel-ottl"]
    }
  }
}
```

### Security-First Mode
Enable enhanced security checking:

```json
{
  "security": {
    "mode": "strict",
    "scanForPII": true,
    "requireSanitization": true,
    "blockSensitivePatterns": true
  }
}
```

## Troubleshooting

### Skills Not Loading
1. Verify installation: `npx skills list`
2. Check `.claude/settings.json` syntax
3. Restart Claude Code
4. Clear skill cache: `npx skills cache clear`

### Missing Context
1. Ensure `CLAUDE.md` exists in project root
2. Check `.claude/settings.json` configuration
3. Verify skill permissions in Claude Code settings

### Inconsistent Suggestions
1. Update project context in `CLAUDE.md`
2. Specify your technology stack clearly
3. Use more specific prompts
4. Reference specific skills by name

## Best Practices

### 1. Keep Context Updated
Regularly update `CLAUDE.md` as your project evolves:

```markdown
## Recent Changes
- Added custom metrics for business KPIs
- Implemented OTTL rules for PII redaction
- Deployed collector in gateway mode
- Created Dynatrace SLOs for API availability
```

### 2. Use Specific Prompts
Instead of: "Add observability"

Use: "Add OpenTelemetry tracing to this Express.js endpoint with span attributes following semantic conventions and PII protection for user data"

### 3. Leverage Workspace Memory
Reference previous implementations:

"Use the same PII protection pattern we implemented for the user service, but adapt it for payment processing"

### 4. Iterate and Refine
Start with basic patterns, then enhance:

1. Basic instrumentation
2. Add custom metrics
3. Implement security measures
4. Optimize for performance
5. Add business context

## Next Steps

1. ✅ Complete this setup
2. 📚 Review the [Skills Documentation](../skills/)
3. 🧪 Try the [Examples](../examples/)
4. 🎥 Watch [IsItObservable tutorials](https://www.youtube.com/@IsItObservable)

Need help? [Open an issue](https://github.com/henrikrexed/observability-agent-skills/issues) or ask Claude Code: "How do I troubleshoot my OpenTelemetry setup?"