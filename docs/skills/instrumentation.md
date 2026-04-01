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

# OTel Instrumentation Skill

Expert guidance for implementing high-quality OpenTelemetry instrumentation across 5 languages.

For full details, see the [SKILL.md](https://github.com/henrikrexed/observability-agent-skills/blob/main/skills/otel-instrumentation/SKILL.md).

## Supported Languages

- [Node.js](../../skills/otel-instrumentation/rules/sdks/nodejs.md) — Express, Fastify, NestJS
- [Go](../../skills/otel-instrumentation/rules/sdks/go.md) — net/http, gin, echo
- [Python](../../skills/otel-instrumentation/rules/sdks/python.md) — Flask, Django, FastAPI
- [Java](../../skills/otel-instrumentation/rules/sdks/java.md) — Spring Boot, javaagent
- [.NET](../../skills/otel-instrumentation/rules/sdks/dotnet.md) — ASP.NET Core

## Key Topics

- Resource attributes and service identity
- Span naming, kind, and status conventions
- Metrics instrument types and cardinality control
- Structured logging with trace correlation
- PII and sensitive data protection
