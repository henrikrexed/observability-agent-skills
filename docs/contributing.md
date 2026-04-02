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

# Contributing

Contributions welcome! Here's how to get involved:

1. **Fork** the repository
2. **Create a branch** for your changes
3. **Follow the AgentSkills format** (SKILL.md + rules/)
4. **Submit a PR** with a clear description

## Adding a New Skill

1. Create `skills/<skill-name>/SKILL.md` with the standard frontmatter
2. Add rules in `skills/<skill-name>/rules/`
3. Add documentation in `docs/skills/`
4. Update `mkdocs.yml` navigation

## Guidelines

- Keep rules concise and actionable
- Include code examples for every concept
- Reference OTel semantic conventions properly
- Test with at least one AI coding assistant
