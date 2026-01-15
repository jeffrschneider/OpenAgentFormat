# Open Agent Format (OAF) Specification

**Version:** 1.2.0
**Date:** 2026-01-15
**Status:** Draft

---

## Overview

The Open Agent Format (OAF) is an open, interoperable format for defining AI agents with their instructions, compositions, and configurations. OAF is designed to work across multiple agent harnesses while maintaining a single canonical representation.

### Design Principles

1. **Filesystem as Source of Truth** - The directory structure and files define the agent
2. **Interoperable** - Works across Claude Code, Goose, Deep Agents, Letta, and other harnesses
3. **Composable** - Agents reference skills, packs, weblets, MCPs, and other agents
4. **Version-aware** - Supports semantic versioning and version history
5. **Human-readable** - Markdown for instructions, YAML for metadata
6. **Harness-agnostic** - Core format independent of any specific harness

---

## Directory Structure

An agent in OAF is a **directory** containing multiple files and subdirectories.

### Complete Structure (with all optional directories)

```
vendor-name/agent-name/
├── AGENTS.md                    # Main manifest (required)
├── README.md                    # Human documentation (generated if absent)
├── LICENSE                      # License file (generated if absent)
│
├── versions/                    # Version history (optional)
│   ├── v1.0.0/
│   │   └── AGENTS.md
│   ├── v1.1.0/
│   │   └── AGENTS.md
│   └── v2.0.0/
│       └── AGENTS.md
│
├── skills/                      # Local/custom skills (optional)
│   ├── custom-skill-1/
│   │   ├── SKILL.md            # Skill manifest (required)
│   │   ├── resources/          # Data files, configs (optional per agentskills.io)
│   │   │   ├── config.json
│   │   │   └── data.csv
│   │   ├── scripts/            # Executable scripts (optional per agentskills.io)
│   │   │   ├── setup.sh
│   │   │   └── run.py
│   │   └── assets/             # Images, diagrams (optional per agentskills.io)
│   │       └── diagram.png
│   └── custom-skill-2/
│       └── SKILL.md
│
├── mcp-configs/                 # MCP server configurations (optional)
│   ├── filesystem/
│   │   ├── ActiveMCP.json      # Tool subset selection
│   │   └── config.yaml         # Connection config
│   ├── database/
│   │   ├── ActiveMCP.json
│   │   └── config.yaml
│   └── stripe-api/
│       ├── ActiveMCP.json
│       └── config.yaml
│
├── examples/                    # Usage examples (optional)
│   ├── debugging-workflow.md
│   └── feature-implementation.md
│
├── tests/                       # Test scenarios (optional)
│   ├── unit-tests.yaml
│   └── integration-tests.yaml
│
├── docs/                        # Additional documentation (optional)
│   ├── architecture.md
│   └── deployment.md
│
└── assets/                      # Agent-level media files (optional)
    ├── logo.png
    └── architecture-diagram.png
```

### Minimal Structure

At minimum, an OAF agent requires only:

```
vendor-name/agent-name/
└── AGENTS.md
```

All other files and directories are optional and generated as needed.

---

## AGENTS.md Format

The `AGENTS.md` file is the primary manifest for an OAF agent.

### Structure

```markdown
---
# === IDENTITY (Required) ===

name: "Display Name"
vendorKey: "vendor-namespace"
agentKey: "agent-identifier"
version: "1.0.0"
slug: "vendor-namespace/agent-identifier"

# === METADATA (Required) ===

description: "Brief description of agent purpose and capabilities"
author: "@vendor-handle"
license: "MIT"
tags: ["tag1", "tag2", "tag3"]

# === COMPOSITION (Optional) ===

# Skills - References to skills (registry or local)
skills:
  - vendor: "anthropic"
    skill: "web-search"
    version: "1.2.0"
    source: "registry"        # "registry" or "local"
    required: true            # true or false
  - vendor: "local"
    skill: "custom-tool"
    version: "1.0.0"
    source: "local"           # References ./skills/custom-tool/

# Packs - Collections of skills
packs:
  - vendor: "langchain"
    pack: "python-dev-tools"
    version: "1.0.0"
    required: false

# Weblets - Web-based tools/interfaces
weblets:
  - vendor: "stripe"
    weblet: "payment-api"
    version: "2.0.0"
    launch: "onDemand"        # "onDemand", "background", "foreground"

# MCP Servers - Model Context Protocol servers
mcpServers:
  - vendor: "block"
    server: "filesystem"
    version: "1.0.0"
    configDir: "mcp-configs/filesystem"  # Contains ActiveMCP.json + config.yaml
    required: true

# Sub-Agents - Nested agent delegation
agents:
  - vendor: "openai"
    agent: "code-reviewer"
    version: "1.5.0"
    role: "reviewer"
    delegations: ["code-quality", "security-check"]
    required: false

# === ORCHESTRATION (Optional) ===

orchestration:
  entrypoint: "main"          # Primary agent entrypoint
  fallback: "error-handler"   # Fallback agent on failure
  triggers:
    - event: "code-change"
      action: "review"
    - event: "deployment"
      action: "validate"

# === TOOLS (Optional) ===

# Explicit tool access (Claude Code sub-agent style)
tools: ["Read", "Edit", "Bash", "Glob", "Grep"]

# === CONFIGURATION (Optional) ===

config:
  temperature: 0.7
  max_tokens: 4096
  require_confirmation: false
  tools:
    allowed: ["bash", "python", "edit", "read", "web_fetch"]
    denied: ["system-admin", "network-scan"]

# === MEMORY (Optional, for stateful agents) ===

memory:
  type: "editable"            # "editable" or "read-only"
  blocks:
    personality: "default"
    user_context: "default"

# === MODEL (Optional) ===

# Full format
model:
  provider: "anthropic"
  name: "claude-sonnet-4-5"
  embedding: "voyage-2"

# OR simplified alias format (Claude Code style)
model: "sonnet"  # "sonnet", "opus", or "haiku"

# === HARNESS-SPECIFIC (Optional) ===

harnessConfig:
  claude-code:
    allowed-tools: ["bash", "edit", "read"]
    progressive-disclosure: true
  goose:
    docker-image: "python:3.11"
    environment:
      PYTHON_VERSION: "3.11"
  deep-agents:
    skills-middleware: true
    auto-load: true
  letta:
    stateful: true
    memory-blocks: ["personality", "user_context"]
---

# Agent Purpose

Describe the agent's primary role, expertise, and what problems it solves.

## Core Responsibilities

- Primary responsibility 1
- Primary responsibility 2
- Primary responsibility 3

## Capabilities

### Domain Knowledge
What the agent knows about and can assist with.

### Technical Skills
- Programming languages
- Tools and frameworks
- Protocols and standards

### Operational Skills
- Task planning and decomposition
- Error handling and recovery
- Context management

## Communication Style

How the agent interacts with users:

- **Tone**: Professional, friendly, concise
- **Verbosity**: Detailed explanations with examples
- **Format**: Structured responses with code blocks
- **Language**: Technical but accessible

## Decision-Making Framework

How the agent approaches problems:

1. **Analysis**: Understand requirements and constraints
2. **Planning**: Break down into subtasks
3. **Execution**: Implement with best practices
4. **Validation**: Test and verify results
5. **Iteration**: Refine based on feedback

## Behavioral Guidelines

### Do:
- Always explain reasoning
- Ask clarifying questions when uncertain
- Provide multiple options when appropriate
- Document code and decisions
- Follow project conventions

### Don't:
- Make assumptions about requirements
- Skip error handling
- Ignore security best practices
- Override user preferences without asking

## Tool Usage Patterns

Describe how the agent uses available tools.

### File Operations
When and how to read, write, edit files.

### Code Execution
Safe execution practices, sandboxing, validation.

### External APIs
Rate limiting, error handling, data validation.

## Delegation Strategy

When this agent delegates to sub-agents or skills:

- **Condition**: Describe when delegation occurs
- **Handoff**: How context is provided
- **Monitoring**: How progress is tracked
- **Integration**: How results are merged

## Limitations & Boundaries

- List limitations
- Define boundaries
- Specify constraints
- Note restrictions

## Examples

Provide usage examples inline or reference `examples/` directory.

### Example 1: [Scenario Name]
```markdown
**User**: [User request]

**Agent**: [Agent response with reasoning]
```

### Example 2: [Scenario Name]
```markdown
**User**: [User request]

**Agent**: [Agent response with steps]
```

## Version History

- **1.0.0** (2026-01-15): Initial release
- **1.1.0** (TBD): Planned features

## License

Specify license or reference LICENSE file.

## Support

Contact information, links to documentation, community channels.
```

---

## Instruction Formats

OAF supports two instruction formats in the Markdown body (after the YAML frontmatter):

### Structured Format (Main Agents)

Use structured Markdown with sections for complex, full-featured agents:

```markdown
---
name: "Python Assistant"
# ... frontmatter ...
---

# Agent Purpose
Detailed description...

## Capabilities
### Domain Knowledge
...

## Communication Style
...
```

**Best for:** Main agents, complex agents, agents requiring extensive documentation.

### Simplified Format (Sub-Agents)

Use direct system prompt for specialized, focused sub-agents (Claude Code style):

```markdown
---
name: "code-reviewer"
description: "Reviews code for quality and best practices"
tools: ["Read", "Glob", "Grep"]
model: "sonnet"
---

You are a code reviewer. When invoked, analyze the code and provide specific, actionable feedback on quality, security, and best practices.
```

**Best for:** Sub-agents, specialized helpers, task-specific agents.

**Detection:** If the body starts with `#`, treat as structured format. Otherwise, treat as direct system prompt.

---

## Field Definitions

### Identity Fields (Required)

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Display name (1-100 chars) |
| `vendorKey` | string | Publisher namespace (kebab-case) |
| `agentKey` | string | Agent identifier (kebab-case) |
| `version` | string | Semantic version (e.g., "1.0.0") |
| `slug` | string | Unique identifier: `vendorKey/agentKey` |

### Metadata Fields (Required)

| Field | Type | Description |
|-------|------|-------------|
| `description` | string | Brief description (50-500 chars) |
| `author` | string | Author handle or name (e.g., "@vendor") |
| `license` | string | SPDX license identifier (e.g., "MIT") |
| `tags` | array[string] | Categorization tags |

### Composition Fields (Optional)

#### Skills
| Field | Type | Description |
|-------|------|-------------|
| `vendor` | string | Skill vendor namespace |
| `skill` | string | Skill identifier |
| `version` | string | Semantic version or version constraint |
| `source` | string | "registry" or "local" |
| `required` | boolean | Whether skill is mandatory |

#### Packs
| Field | Type | Description |
|-------|------|-------------|
| `vendor` | string | Pack vendor namespace |
| `pack` | string | Pack identifier |
| `version` | string | Semantic version |
| `required` | boolean | Whether pack is mandatory |

#### Weblets
| Field | Type | Description |
|-------|------|-------------|
| `vendor` | string | Weblet vendor namespace |
| `weblet` | string | Weblet identifier |
| `version` | string | Semantic version |
| `launch` | string | Launch mode: "onDemand", "background", "foreground" |

#### MCP Servers
| Field | Type | Description |
|-------|------|-------------|
| `vendor` | string | MCP vendor namespace |
| `server` | string | Server identifier |
| `version` | string | Semantic version |
| `configDir` | string | Path to config directory (contains ActiveMCP.json + config.yaml) |
| `required` | boolean | Whether MCP server is mandatory |

**Note:** Tool selection is now managed via `ActiveMCP.json` in the config directory to avoid context overflow.

#### Sub-Agents
| Field | Type | Description |
|-------|------|-------------|
| `vendor` | string | Agent vendor namespace |
| `agent` | string | Agent identifier |
| `version` | string | Semantic version |
| `role` | string | Role in composition (e.g., "reviewer") |
| `delegations` | array[string] | Tasks delegated to this agent |
| `required` | boolean | Whether sub-agent is mandatory |

### Orchestration Fields (Optional)

| Field | Type | Description |
|-------|------|-------------|
| `entrypoint` | string | Primary agent entrypoint |
| `fallback` | string | Fallback agent on failure |
| `triggers` | array[object] | Event-action mappings |

### Tools Field (Optional)

| Field | Type | Description |
|-------|------|-------------|
| `tools` | array[string] | Explicit tool access list (Claude Code style). Example: `["Read", "Edit", "Bash", "Glob", "Grep"]` |

**Note:** The `tools` field provides simple, explicit tool access control (Claude Code sub-agent style). For more complex tool configuration, use `config.tools.allowed` and `config.tools.denied`.

### Configuration Fields (Optional)

| Field | Type | Description |
|-------|------|-------------|
| `temperature` | number | Model temperature (0.0-1.0) |
| `max_tokens` | number | Maximum output tokens |
| `require_confirmation` | boolean | Require user confirmation for actions |
| `tools.allowed` | array[string] | Allowed tools |
| `tools.denied` | array[string] | Denied tools |

### Memory Fields (Optional)

| Field | Type | Description |
|-------|------|-------------|
| `type` | string | "editable" or "read-only" |
| `blocks` | object | Memory block definitions |

### Model Fields (Optional)

The `model` field supports two formats:

**Full Format (Object):**

| Field | Type | Description |
|-------|------|-------------|
| `provider` | string | Model provider (e.g., "anthropic") |
| `name` | string | Model name (e.g., "claude-sonnet-4-5") |
| `embedding` | string | Embedding model name |

Example:
```yaml
model:
  provider: "anthropic"
  name: "claude-sonnet-4-5"
  embedding: "voyage-2"
```

**Simplified Format (String Alias):**

| Value | Description |
|-------|-------------|
| `"sonnet"` | Claude Sonnet (latest) |
| `"opus"` | Claude Opus (latest) |
| `"haiku"` | Claude Haiku (latest) |

Example:
```yaml
model: "sonnet"
```

**Best for:** Sub-agents and simple configurations (Claude Code style)

### Harness-Specific Configuration (Optional)

The `harnessConfig` section allows harness providers to specify custom configuration for their platform.

**Format:**
```yaml
harnessConfig:
  vendor-product:
    # Harness-specific fields in any format
    key: value
    nested:
      field: value
```

Each harness provider identifies themselves by `vendor/product` key (e.g., `claude-code`, `goose`, `deep-agents`, `letta`) and can add any fields they need in any format (YAML, JSON-like structures, arrays, etc.).

**This section is completely free-form and harness-defined.**

---

## Skills Directory Format (AgentSkills.io)

When an agent includes local/custom skills, they are placed in the `skills/` directory following the [AgentSkills.io specification](https://agentskills.io/).

### Skill Directory Structure

```
skills/skill-name/
├── SKILL.md                    # Skill manifest (required)
├── resources/                  # Data files, configs (optional)
│   ├── config.json
│   ├── data.csv
│   └── prompts/
│       └── system.txt
├── scripts/                    # Executable scripts (optional)
│   ├── setup.sh
│   ├── run.py
│   └── cleanup.sh
└── assets/                     # Images, diagrams (optional)
    ├── icon.png
    └── diagram.svg
```

### SKILL.md Format

```yaml
---
name: "skill-name"
description: "Brief description of what this skill does and when to use it"
license: "MIT"
metadata:
  author: "vendor-name"
  version: "1.0.0"
allowed-tools: ["bash", "python", "edit"]
---

# Skill Purpose

Instructions for using this skill.

## When to Use

Trigger conditions.

## Usage

Examples and patterns.
```

---

## MCP Configs Directory

MCP server configurations are stored in `mcp-configs/` with each MCP server having its own subdirectory containing two files:

1. **ActiveMCP.json** - Tool subset selection (avoids context overflow)
2. **config.yaml** - Connection and permission configuration

### Why ActiveMCP.json?

MCP servers can expose dozens or hundreds of tools. Including all tool descriptions in the agent's context window can cause:
- **Context overflow** - Exceeding model token limits
- **Degraded performance** - Too many irrelevant tools to choose from
- **Increased latency** - Larger context = slower processing

**ActiveMCP.json** allows agents to specify only the subset of tools they need from each MCP server, keeping context lean and focused.

**Inspired by:** Langchain's custom `tools.json` approach for selective MCP tool access.

### Directory Structure

```
mcp-configs/
├── filesystem/
│   ├── ActiveMCP.json      # Tool subset selection
│   └── config.yaml         # Connection config
└── database/
    ├── ActiveMCP.json
    └── config.yaml
```

### ActiveMCP.json Format

**Purpose:** Specify which tools to make available from the MCP server.

```json
{
  "vendor": "block",
  "server": "filesystem",
  "version": "1.0.0",
  "selectedTools": [
    {
      "name": "read_file",
      "enabled": true,
      "description": "Read contents of a file",
      "required": true
    },
    {
      "name": "write_file",
      "enabled": true,
      "description": "Write content to a file",
      "required": true
    },
    {
      "name": "list_directory",
      "enabled": true,
      "description": "List files in a directory",
      "required": false
    }
  ],
  "excludedTools": [
    "delete_file",
    "move_file",
    "chmod"
  ],
  "contextStrategy": "subset"
}
```

**Fields:**

| Field | Type | Description |
|-------|------|-------------|
| `vendor` | string | MCP server vendor |
| `server` | string | MCP server name |
| `version` | string | MCP server version |
| `selectedTools` | array | Tools to include (with metadata) |
| `selectedTools[].name` | string | Tool name |
| `selectedTools[].enabled` | boolean | Whether tool is active |
| `selectedTools[].description` | string | Custom description (optional, overrides server default) |
| `selectedTools[].required` | boolean | Whether tool is required for agent operation |
| `excludedTools` | array[string] | Tools to explicitly exclude |
| `contextStrategy` | string | "subset" (only selected tools) or "all" (all tools) |

### config.yaml Format

**Purpose:** Connection details, authentication, permissions.

```yaml
vendor: "block"
server: "filesystem"
version: "1.0.0"

# Connection
connection:
  type: "sse"                    # "sse", "http", "stdio"
  url: "http://localhost:8811/sse"
  timeout: 60

# Authentication (optional)
auth:
  type: "bearer"                 # "bearer", "api-key", "oauth"
  token: "${FILESYSTEM_TOKEN}"   # Environment variable reference

# Permissions (optional)
permissions:
  allow_paths: ["/workspace", "/tmp"]
  deny_paths: ["/system", "/etc"]
  max_file_size: "10MB"
  read_only: false

# Rate limiting (optional)
rate_limit:
  requests_per_minute: 60
  burst: 10
```

**Fields:**

| Field | Type | Description |
|-------|------|-------------|
| `vendor` | string | MCP server vendor |
| `server` | string | MCP server name |
| `version` | string | MCP server version |
| `connection.type` | string | Connection protocol: "sse", "http", "stdio" |
| `connection.url` | string | Server endpoint URL |
| `connection.timeout` | number | Request timeout (seconds) |
| `auth.type` | string | Auth method: "bearer", "api-key", "oauth" |
| `auth.token` | string | Auth token (supports env vars: `${VAR_NAME}`) |
| `permissions` | object | Access control rules (server-specific) |
| `rate_limit` | object | Rate limiting configuration |

### Example: Filesystem MCP

**mcp-configs/filesystem/ActiveMCP.json**
```json
{
  "vendor": "block",
  "server": "filesystem",
  "version": "1.0.0",
  "selectedTools": [
    {
      "name": "read_file",
      "enabled": true,
      "description": "Read file contents for analysis",
      "required": true
    },
    {
      "name": "list_directory",
      "enabled": true,
      "description": "List directory contents",
      "required": false
    }
  ],
  "excludedTools": ["delete_file", "chmod", "chown"],
  "contextStrategy": "subset"
}
```

**mcp-configs/filesystem/config.yaml**
```yaml
vendor: "block"
server: "filesystem"
version: "1.0.0"

connection:
  type: "sse"
  url: "http://localhost:8811/sse"
  timeout: 60

permissions:
  allow_paths: ["/workspace", "/project"]
  deny_paths: ["/system", "/etc", "/root"]
  read_only: false
```

---

## Version History Support

Agents can maintain version history in the `versions/` directory.

**Structure:**
```
versions/
├── v1.0.0/
│   └── AGENTS.md
├── v1.1.0/
│   └── AGENTS.md
└── v2.0.0/
    └── AGENTS.md
```

The root `AGENTS.md` represents the current/latest version.

---

## File Generation Guidelines

When generating an OAF agent directory:

### Required Files
1. **AGENTS.md** - Main manifest with all metadata and instructions

### Auto-Generated Files (if missing)
2. **README.md** - Human-readable documentation derived from AGENTS.md
3. **LICENSE** - License file based on `license` field in AGENTS.md

### Conditional Directories (only if data exists)
4. **skills/** - Only if agent has local skills (`source: "local"`)
5. **mcp-configs/** - Only if agent has MCP server references
6. **versions/** - Only if version history should be preserved
7. **examples/** - Only if examples exist
8. **tests/** - Only if test scenarios exist
9. **docs/** - Only if additional documentation exists
10. **assets/** - Only if media files exist

---

## Compliance & Standards

The Open Agent Format complies with and builds upon:

- **[AGENTS.md](https://agents.md/)** - OpenAI's universal agent standard
- **[AgentSkills.io](https://agentskills.io/)** - Anthropic/OpenAI skills specification
- **Semantic Versioning** - Version constraints follow [semver.org](https://semver.org/)
- **SPDX Licenses** - License identifiers follow [spdx.org](https://spdx.org/)

---

## Export Compatibility

OAF is designed to export to multiple harness formats:

| Harness | Export Format | Notes |
|---------|---------------|-------|
| **Claude Code** | `~/.claude/skills/vendor/agent/` | Converts to SKILL.md format |
| **Goose** | `~/.config/goose/extensions/vendor/agent/` | Generates AGENTS.md |
| **Deep Agents** | `~/.deepagents/agent-name/` | Splits into agent.md + skills/ |
| **Letta** | `agent-name.af` | Converts to Agent File JSON |

Export procedures and tooling specifics are implementation-defined and not part of this specification.

---

## Validation (Informative)

While validation is implementation-defined, OAF suggests validating:

1. **Required fields present** - Identity and metadata fields exist
2. **Valid formats** - Semantic versions, kebab-case identifiers
3. **YAML validity** - Frontmatter parses correctly
4. **Markdown structure** - At least one `##` heading in instructions

Advanced validation (circular dependencies, version conflicts, reference resolution) is the responsibility of agent harnesses and tooling.

---

## Examples

### Minimal Agent

```
minimal-agent/
└── AGENTS.md
```

**AGENTS.md:**
```yaml
---
name: "Simple Assistant"
vendorKey: "acme"
agentKey: "simple"
version: "1.0.0"
slug: "acme/simple"
description: "A simple helpful assistant"
author: "@acme"
license: "MIT"
tags: ["assistant"]
---

# Agent Purpose

I am a simple helpful assistant.

## Core Responsibilities

- Answer questions
- Provide assistance
```

### Simplified Sub-Agent (Claude Code Style)

```
code-reviewer/
└── AGENTS.md
```

**AGENTS.md:**
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
tags: ["code-review", "quality"]
tools: ["Read", "Glob", "Grep"]
model: "sonnet"
---

You are a code reviewer. When invoked, analyze the code and provide specific, actionable feedback on:

- Code quality and readability
- Security vulnerabilities
- Performance issues
- Best practices adherence
- Potential bugs

Be concise but thorough. Prioritize critical issues first.
```

**Note:** This simplified format is ideal for sub-agents - minimal frontmatter, direct system prompt, focused purpose.

### Full-Featured Agent

```
advanced-agent/
├── AGENTS.md
├── README.md
├── LICENSE
├── versions/
│   ├── v1.0.0/
│   │   └── AGENTS.md
│   └── v2.0.0/
│       └── AGENTS.md
├── skills/
│   └── custom-search/
│       ├── SKILL.md
│       ├── resources/
│       │   └── config.json
│       ├── scripts/
│       │   └── search.py
│       └── assets/
│           └── icon.png
├── mcp-configs/
│   └── filesystem.yaml
├── examples/
│   └── usage.md
├── tests/
│   └── test-scenarios.yaml
├── docs/
│   └── architecture.md
└── assets/
    └── logo.png
```

---

## Changelog

- **1.2.0** (2026-01-15): MCP tool subset selection (ActiveMCP.json)
  - Added `ActiveMCP.json` for selective MCP tool access (avoids context overflow)
  - Restructured `mcp-configs/` to use subdirectories (one per MCP server)
  - Separated tool selection (ActiveMCP.json) from connection config (config.yaml)
  - Added `selectedTools` array with per-tool metadata (enabled, description, required)
  - Added `excludedTools` array for explicit tool exclusion
  - Added `contextStrategy` field ("subset" vs "all")
  - Inspired by Langchain's custom tools.json approach

- **1.1.0** (2026-01-15): Claude Code sub-agent support
  - Added `tools` field for explicit tool access control
  - Added model alias support (`sonnet`, `opus`, `haiku`)
  - Added simplified instruction format for sub-agents
  - Documented structured vs simplified format detection
  - Added sub-agent example

- **1.0.0** (2026-01-15): Initial Open Agent Format specification
  - Defined directory structure
  - Defined AGENTS.md format
  - Specified AgentSkills.io compliance
  - Added harness-specific configuration section
  - Documented version history support

---

## License

This specification is released under CC0 1.0 Universal (Public Domain).

## Contributing

This is a living specification. Feedback, proposals, and contributions are welcome.

## References

- [AGENTS.md Standard](https://agents.md/)
- [AgentSkills.io Specification](https://agentskills.io/)
- [Semantic Versioning](https://semver.org/)
- [SPDX License List](https://spdx.org/licenses/)
- [Agentic AI Foundation](https://www.linuxfoundation.org/press/linux-foundation-announces-the-formation-of-the-agentic-ai-foundation)
