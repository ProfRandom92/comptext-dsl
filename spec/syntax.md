# CompText DSL - Formal Syntax Specification

## Version 1.1.0 - MCP & Codex Aligned

## Overview

CompText DSL is a token-efficient language designed for LLM interactions. This document defines the formal syntax and semantic rules, including integration with MCP (Model Context Protocol) and Codex Skills.

## Grammar Notation

This specification uses **Extended Backus-Naur Form (EBNF)** notation:

- `=` defines a production rule
- `|` separates alternatives
- `[ ]` indicates optional elements
- `{ }` indicates repetition (zero or more times)
- `(  )` groups elements
- `" "` denotes terminal symbols (literals)

## Syntax Rules

### 1. Commands

```ebnf
command ::= identifier [ parameters ] [ options ]
identifier ::= (letter | "_") { letter | digit | "-" | "_" }
```

**Examples:**
```comptext
fetch-data(source="api", limit=100)
transform(type="json")
store
```

### 2. Skill Invocations (NEW in v1.1)

```ebnf
skill_invocation ::= "$" identifier [ parameters ]
```

**Examples:**
```comptext
$code-review
$draft-commit-message(style="conventional")
$analyze-tests(coverage=true)
```

### 3. MCP Tool Definitions (NEW in v1.1)

```ebnf
tool_definition ::= "tool" identifier "{" tool_body "}"
tool_body ::= tool_name tool_description [ tool_input_schema ]
tool_name ::= "name" ":" string
tool_description ::= "description" ":" string
tool_input_schema ::= "inputSchema" ":" json_schema
```

**Example:**
```comptext
tool fetch-data {
  name: "fetch-data"
  description: "Fetches data from external sources"
  inputSchema: {
    type: "object",
    properties: {
      source: { type: "string" },
      limit: { type: "number" }
    },
    required: ["source"]
  }
}
```

### 4. MCP Tasks (NEW in v1.1)

```ebnf
task_definition ::= "task" identifier "{" task_body "}"
task_body ::= task_name [ task_description ] [ task_timeout ] task_handler
task_timeout ::= "timeout" ":" number
task_handler ::= "handler" ":" ( identifier | string )
```

**Example:**
```comptext
task data-processing {
  name: "data-processing"
  description: "Processes large datasets"
  timeout: 300000
  handler: "process_data"
}
```

### 5. MCP Resources (NEW in v1.1)

```ebnf
resource_ref ::= "@" identifier [ "/" path_segment { "/" path_segment } ]
path_segment ::= identifier | string
```

**Examples:**
```comptext
@database/users/schema
@config/settings
@templates/report
```

### 6. Parameters

```ebnf
parameters ::= "(" [ param-list ] ")"
param-list ::= param { "," param }
param ::= key "=" value | value
key ::= identifier
```

**Type System:**
- `string`: Text values in double quotes
- `number`: Integer or floating-point
- `boolean`: `true` or `false`
- `array`: Ordered collection `[value, ...]`
- `object`: Key-value pairs `{key: value, ...}`

### 7. Module System

```ebnf
module-import ::= "import" string [ "as" identifier ]
```

**Example:**
```comptext
import "data-processing" as dp
dp.clean(method="robust")
```

### 8. Variables

```ebnf
variable_assignment ::= identifier ":=" value
reference ::= "$" identifier
```

**Example:**
```comptext
api_url := "https://api.example.com"
fetch-data(source=$api_url)
```

### 9. Comments

```ebnf
line_comment ::= "#" { any_char_except_newline }
block_comment ::= "/*" { any_char } "*/"
```

**Examples:**
```comptext
# Single line comment

/*
  Multi-line
  block comment
*/
```

## Semantic Rules

### Scoping

1. **Global Scope**: Module imports and top-level commands
2. **Module Scope**: Identifiers within imported modules
3. **Parameter Scope**: Values within parameter lists
4. **Tool/Skill Scope**: Definitions within tool/skill blocks

### Type Safety

- Strong typing with implicit conversions
- Type mismatches result in compilation errors
- Array and object types can be nested

### Execution Model

1. Sequential execution of commands
2. Module imports are resolved first
3. Tool and skill definitions are registered
4. Commands and skill invocations execute in declaration order
5. Tasks execute asynchronously

## MCP Protocol Mapping

### Tool Registration

CompText tool definitions map to MCP `tools/list` responses:

| CompText | MCP JSON-RPC |
|----------|--------------|
| `tool name` | `tools[].name` |
| `description` | `tools[].description` |
| `inputSchema` | `tools[].inputSchema` |

### Tool Invocation

```comptext
use:tool-name(param="value")
```

Maps to:

```json
{
  "jsonrpc": "2.0",
  "method": "tools/call",
  "params": {
    "name": "tool-name",
    "arguments": { "param": "value" }
  }
}
```

### Resource Access

```comptext
@database/users
```

Maps to:

```
resource://database/users
```

## Codex Skills Mapping

### Skill Invocation

```comptext
$skill-name(param="value")
```

Resolves to SKILL.md in:
1. `.codex/skills/skill-name/SKILL.md`
2. `~/.codex/skills/skill-name/SKILL.md`

### Skill Definition

```comptext
skill my-skill {
  name: "my-skill"
  description: "..."
  instructions: "..."
}
```

Generates:

```markdown
---
name: my-skill
description: ...
---
[instructions content]
```

## Error Handling

### Syntax Errors

```
Error: Unexpected token ',' at line 5, column 12
Expected: identifier or ')'
```

### Type Errors

```
Error: Type mismatch in parameter 'limit'
Expected: number
Got: string
```

### MCP Errors

| Code | Name | Description |
|------|------|-------------|
| -32700 | Parse Error | Invalid JSON |
| -32600 | Invalid Request | Invalid JSON-RPC |
| -32601 | Method Not Found | Unknown method |
| -32602 | Invalid Params | Invalid parameters |
| -32603 | Internal Error | Server error |

## Best Practices

1. **Naming**: Use kebab-case for multi-word identifiers
2. **Parameters**: Always quote string values
3. **Modularity**: Group related commands into modules
4. **Comments**: Use `#` for single-line, `/* */` for multi-line
5. **Skills**: Use `$` prefix for Codex skill invocations
6. **Resources**: Use `@` prefix for MCP resource references

## Token Efficiency

CompText DSL achieves **90-95% token reduction** compared to natural language prompts:

| Approach | Tokens | Reduction |
|----------|--------|-----------|
| Natural Language | 150-200 | - |
| CompText DSL | 10-15 | 92-95% |

## Migration Guide

### From v1.0 to v1.1

| v1.0 | v1.1 | Notes |
|------|------|-------|
| `use:command` | `use:command` | Still supported |
| - | `$skill-name` | New skill syntax |
| - | `tool {...}` | New MCP tool definition |
| - | `task {...}` | New MCP task definition |
| - | `@resource` | New MCP resource syntax |

### Backward Compatibility

```comptext
config {
  legacy-use-syntax: true  # Enable v1.0 compatibility
}
```

## See Also

- [MCP Integration](mcp-integration.md)
- [Codex Skills](codex-skills.md)
- [Architecture](../docs/ARCHITECTURE.md)
- [Examples](../EXAMPLES.md)
