# CompText DSL Architecture

## Version 1.1.0 - MCP & Codex Aligned

## Overview

CompText DSL is a token-efficient domain-specific language designed for seamless integration with modern AI tooling ecosystems, including the **Model Context Protocol (MCP)** and **OpenAI Codex CLI**.

## Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                      CompText DSL Layer                         │
├─────────────────────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────────┐  │
│  │   Parser    │  │  Compiler   │  │       Runtime           │  │
│  │             │  │             │  │                         │  │
│  │ • Tokenizer │  │ • AST Opt.  │  │ • Command Resolution    │  │
│  │ • Syntax    │  │ • Code Gen  │  │ • Module Loading        │  │
│  │ • AST Gen   │  │ • Output    │  │ • Context Injection     │  │
│  └─────────────┘  └─────────────┘  └─────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Integration Layer                            │
├────────────────────────┬────────────────────────────────────────┤
│   MCP Protocol         │        Codex Skills                    │
│   (JSON-RPC 2.0)       │        (SKILL.md)                      │
├────────────────────────┼────────────────────────────────────────┤
│ • Tool Definitions     │ • Skill Invocation ($)                 │
│ • Resource Access (@)  │ • Skill Generation                     │
│ • Tasks (Async)        │ • AGENTS.md Support                    │
│ • OAuth 2.1 Auth       │ • Parameter Schemas                    │
└────────────────────────┴────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                    External Systems                             │
├─────────────────────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────────┐  │
│  │ MCP Servers │  │ Codex CLI   │  │    LLM Clients          │  │
│  │             │  │             │  │                         │  │
│  │ • Claude    │  │ • Terminal  │  │ • ChatGPT               │  │
│  │ • Custom    │  │ • IDE Ext.  │  │ • Claude                │  │
│  └─────────────┘  └─────────────┘  └─────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
```

## Core Components

### Parser

- **Tokenization**: Breaks input into tokens
- **Syntax Analysis**: Validates grammar rules (EBNF v1.1)
- **AST Generation**: Produces Abstract Syntax Tree

### Compiler

- **AST Optimization**: Reduces redundancy
- **Code Generation**: Produces target output (JSON-RPC, SKILL.md)
- **Output Formatting**: Formats for target platform

### Runtime

- **Command Resolution**: Resolves `use:` and `$skill` references
- **Module Loading**: Imports and manages modules
- **Context Injection**: Injects runtime context

## MCP Integration

### Protocol Support

CompText DSL fully supports the [MCP November 2025 Specification](https://modelcontextprotocol.io/specification/2025-11-25):

| Feature | Status | Description |
|---------|--------|-------------|
| JSON-RPC 2.0 | ✅ | Full protocol support |
| Tools | ✅ | `tool` definitions, `tools/list`, `tools/call` |
| Resources | ✅ | `@resource` syntax, URI mapping |
| Tasks | ✅ | Async operations, progress notifications |
| OAuth 2.1 | ✅ | Authorization framework |
| Resource Indicators | ✅ | RFC 8707 compliance |

### Tool Definition Flow

```
CompText DSL          MCP Server           LLM Client
     │                    │                     │
     │ tool {...}         │                     │
     │───────────────────>│                     │
     │                    │ tools/list          │
     │                    │<────────────────────│
     │                    │ [tools array]       │
     │                    │────────────────────>│
     │                    │                     │
     │ use:tool(...)      │                     │
     │───────────────────>│                     │
     │                    │ tools/call          │
     │                    │<────────────────────│
     │                    │ result              │
     │                    │────────────────────>│
```

### Task Lifecycle

```
┌──────────┐     ┌──────────┐     ┌──────────┐     ┌──────────┐
│ pending  │────>│ running  │────>│completed │     │  failed  │
└──────────┘     └──────────┘     └──────────┘     └──────────┘
                      │                                  ▲
                      │          ┌──────────┐           │
                      └─────────>│cancelled │───────────┘
                                 └──────────┘
```

## Codex Skills Integration

### Skill Resolution

CompText DSL supports Codex's skill resolution hierarchy:

1. **Repo-scoped**: `.codex/skills/<name>/SKILL.md`
2. **User-scoped**: `~/.codex/skills/<name>/SKILL.md`
3. **Remote Codex**: CompText public skill repository

### Skill Generation

CompText DSL can generate complete Codex skill directories:

```
Input:  skill my-skill { ... }

Output: .codex/skills/my-skill/
        ├── SKILL.md
        ├── templates/
        ├── schemas/
        └── scripts/
```

### AGENTS.md Support

CompText generates `AGENTS.md` for project-level Codex configuration:

```comptext
agents-config {
  instructions: "..."
  tools: [...]
  focus: [...]
}
```

## Data Flow

### Command Processing

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│  CompText   │     │   Parser    │     │   AST       │
│  Source     │────>│             │────>│             │
└─────────────┘     └─────────────┘     └─────────────┘
                                              │
                    ┌─────────────────────────┘
                    ▼
              ┌─────────────┐     ┌─────────────┐
              │  Compiler   │────>│   Output    │
              └─────────────┘     └─────────────┘
                                        │
              ┌─────────────────────────┼─────────────────────────┐
              ▼                         ▼                         ▼
        ┌───────────┐           ┌───────────┐           ┌───────────┐
        │ JSON-RPC  │           │ SKILL.md  │           │  Context  │
        │ (MCP)     │           │ (Codex)   │           │  (LLM)    │
        └───────────┘           └───────────┘           └───────────┘
```

## Security

### Authentication

- **MCP**: OAuth 2.1 with Protected Resource Metadata
- **Codex**: Inherits from Codex CLI authentication

### Sandboxing

- CompText runtime operates within Codex CLI's OS-level sandbox
- MCP tools execute in isolated server contexts

## Extension Points

### Custom Tools

```comptext
tool custom-tool {
  name: "custom-tool"
  description: "..."
  inputSchema: { ... }
}
```

### Custom Skills

```comptext
skill custom-skill {
  name: "custom-skill"
  description: "..."
  instructions: "..."
}
```

### Module System

```comptext
import "custom-module" as cm
cm.command(...)
```

## Integration Matrix

| Platform | Protocol | Status |
|----------|----------|--------|
| Claude (Anthropic) | MCP | ✅ Full Support |
| Codex CLI | Skills | ✅ Full Support |
| ChatGPT | Skills/MCP | ✅ Full Support |
| Claude Code | MCP | ✅ Full Support |
| Custom MCP Servers | JSON-RPC 2.0 | ✅ Full Support |

## References

- [MCP Specification (Nov 2025)](https://modelcontextprotocol.io/specification/2025-11-25)
- [Codex CLI](https://developers.openai.com/codex/cli/)
- [Codex Skills](https://developers.openai.com/codex/skills/)
- [CompText MCP Server](https://github.com/ProfRandom92/comptext-mcp-server)

## See Also

- [MCP Integration Spec](../spec/mcp-integration.md)
- [Codex Skills Spec](../spec/codex-skills.md)
- [Syntax Specification](../spec/syntax.md)
