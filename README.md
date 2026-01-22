# Open Agent Format (OAF)

**Version:** 0.8.0 | **Status:** Draft

The Open Agent Format (OAF) is an open, interoperable specification for defining AI agents. It provides a standard way to describe agents with their instructions, compositions, and configurations—portable across any agent harness.

## Why OAF?

Today's AI agent ecosystem is fragmented. Each platform defines agents differently, making it difficult to:

- **Share agents** between teams using different tools
- **Migrate agents** when switching platforms
- **Compose agents** from reusable components
- **Distribute agents** in a standard format

OAF solves this by providing a single canonical representation that any harness can import and export.

## Key Design Decisions

1. **Filesystem is the source of truth** — Agent definition lives in directories and files, not databases or APIs

2. **Single required file** — Only `AGENTS.md` is mandatory; everything else is optional, lowering the barrier to entry

3. **YAML frontmatter + Markdown body** — Machine-readable metadata separated from human-readable instructions in one file

4. **Harness-agnostic core** — Designed to work across multiple agent platforms without modification

5. **Composition model** — Agents compose from skills, packs, MCP servers, and sub-agents rather than being monolithic

6. **Vendor/agent slug convention** — Unique identification via `vendorKey/agentKey` pattern (e.g., `acme/code-reviewer`)

7. **Two instruction formats** — Structured sections for main agents vs. direct system prompt for focused sub-agents

8. **ActiveMCP.json for tool subsetting** — Explicit tool selection from MCP servers to prevent context overflow

9. **Well-known URI discovery** — Skills published at `/.well-known/skills/{name}/` for standard discovery

10. **Standard zip packaging** — Distribution uses plain `.zip` files, not a custom archive format

11. **Bundled vs Referenced modes** — Packages can be self-contained or fetch dependencies at install time

12. **Flat agent storage in packages** — No nesting; each agent directory is self-contained with no shared resources

13. **Free-form harnessConfig** — Harness providers can add any configuration they need without spec changes

14. **Model aliases** — Simple strings (`"sonnet"`, `"opus"`, `"haiku"`) alongside full provider/model specs

15. **Semantic versioning throughout** — Agents, skills, packages all use semver

16. **Skills follow AgentSkills.io** — Adopts existing skills specification rather than inventing new

17. **Builds on AGENTS.md standard** — Extends the universal agent standard rather than replacing it

18. **SPDX license identifiers** — Uses standard license naming (MIT, Apache-2.0, etc.)

19. **Validation is informative, not normative** — Spec suggests what to validate but harnesses decide implementation

20. **Export compatibility over lock-in** — Explicitly designed to export to multiple harness formats

## Quick Start

### Minimal Agent

An OAF agent requires just one file:

```
my-agent/
└── AGENTS.md
```

**AGENTS.md:**
```yaml
---
name: "My Assistant"
vendorKey: "acme"
agentKey: "my-assistant"
version: "1.0.0"
slug: "acme/my-assistant"
description: "A helpful assistant for everyday tasks"
author: "@acme"
license: "MIT"
tags: ["assistant", "general"]
---

# Agent Purpose

I help users with everyday tasks, answering questions and providing assistance.

## Core Responsibilities

- Answer questions accurately
- Provide helpful explanations
- Assist with problem-solving
```

### Sub-Agent (Simplified Format)

For focused, single-purpose agents:

```yaml
---
name: "code-reviewer"
vendorKey: "acme"
agentKey: "code-reviewer"
version: "1.0.0"
slug: "acme/code-reviewer"
description: "Reviews code for quality and best practices"
author: "@acme"
license: "MIT"
tags: ["code-review"]
tools: ["Read", "Glob", "Grep"]
model: "sonnet"
---

You are a code reviewer. Analyze code and provide specific, actionable feedback on quality, security, and best practices. Be concise but thorough.
```

## Directory Structure

A full-featured OAF agent can include:

```
agent-name/
├── AGENTS.md                    # Main manifest (required)
├── README.md                    # Human documentation
├── LICENSE                      # License file
├── skills/                      # Local skills
│   └── custom-skill/
│       └── SKILL.md
├── mcp-configs/                 # MCP server configurations
│   └── filesystem/
│       ├── ActiveMCP.json       # Tool subset selection
│       └── config.yaml          # Connection config
├── versions/                    # Version history
│   └── v1.0.0/
│       └── AGENTS.md
├── examples/                    # Usage examples
├── tests/                       # Test scenarios
└── docs/                        # Additional documentation
```

## Composition

OAF agents can compose capabilities from multiple sources:

### Skills
```yaml
skills:
  - name: "web-search"
    source: "https://example.com/.well-known/skills/web-search"
    version: "1.2.0"
    required: true
  - name: "custom-tool"
    source: "local"              # References ./skills/custom-tool/
    version: "1.0.0"
```

### MCP Servers
```yaml
mcpServers:
  - vendor: "block"
    server: "filesystem"
    version: "1.0.0"
    configDir: "mcp-configs/filesystem"
    required: true
```

### Sub-Agents
```yaml
agents:
  - vendor: "acme"
    agent: "code-reviewer"
    version: "1.5.0"
    role: "reviewer"
    delegations: ["code-quality", "security-check"]
```

## Packaging

Distribute agents as standard `.zip` files:

```
my-agent-1.0.0.zip
├── PACKAGE.yaml                 # Package manifest
└── my-agent/
    └── AGENTS.md
```

**PACKAGE.yaml:**
```yaml
format: "oaf-package"
formatVersion: "1.0.0"
version: "1.0.0"

agents:
  - slug: "acme/my-agent"
    version: "1.0.0"

contents:
  mode: "bundled"                # or "referenced"
```

## Tool Subset Selection

Avoid context overflow by selecting only the MCP tools you need:

**mcp-configs/filesystem/ActiveMCP.json:**
```json
{
  "vendor": "block",
  "server": "filesystem",
  "version": "1.0.0",
  "selectedTools": [
    { "name": "read_file", "enabled": true, "required": true },
    { "name": "list_directory", "enabled": true, "required": false }
  ],
  "excludedTools": ["delete_file", "chmod"],
  "contextStrategy": "subset"
}
```

## Skill Discovery

Publish skills at well-known URIs for standard discovery:

```
https://example.com/.well-known/skills/index.json
https://example.com/.well-known/skills/{skill-name}/SKILL.md
```

Reference them in your agent:
```yaml
skills:
  - name: "pdf-processing"
    source: "https://example.com/.well-known/skills/pdf-processing"
    version: "1.0.0"
```

## Specification

For the complete specification, see:

- **[Full Specification (Markdown)](docs/OPEN_AGENT_FORMAT.md)**
- **[Full Specification (HTML)](https://openagentformat.org/spec.html)**

## Standards Compliance

OAF builds upon established standards:

- **[AGENTS.md](https://agents.md/)** — Universal agent standard
- **[AgentSkills.io](https://agentskills.io/)** — Skills specification
- **[Semantic Versioning](https://semver.org/)** — Version constraints
- **[SPDX](https://spdx.org/)** — License identifiers

## Contributing

This is a living specification. Feedback, proposals, and contributions are welcome.

## License

This specification is released under CC0 1.0 Universal (Public Domain).
