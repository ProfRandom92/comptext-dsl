# CompText DSL - MCP Integration Specification

## Version 1.1.0

## Overview

This document specifies how CompText DSL integrates with the **Model Context Protocol (MCP)** as defined in the [November 2025 Specification](https://modelcontextprotocol.io/specification/2025-11-25).

## MCP Protocol Compliance

CompText DSL is designed to be fully compatible with MCP's JSON-RPC 2.0 based protocol, enabling seamless integration with MCP servers and clients.

### Protocol Version

- **MCP Version**: 2025-11-25
- **Transport**: JSON-RPC 2.0
- **Authentication**: OAuth 2.1 (RFC 6749, RFC 6750)

## Tool Definitions

CompText DSL provides native syntax for defining MCP-compatible tools.

### Syntax

```comptext
tool fetch-data {
  name: "fetch-data"
  description: "Fetches data from external sources"
  inputSchema: {
    type: "object",
    properties: {
      source: { type: "string", description: "Data source URL" },
      limit: { type: "number", description: "Maximum records" }
    },
    required: ["source"]
  }
}
```

### JSON-RPC Mapping

The above CompText tool definition maps to the following MCP tool registration:

```json
{
  "jsonrpc": "2.0",
  "method": "tools/list",
  "id": 1
}
```

Response:

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "tools": [
      {
        "name": "fetch-data",
        "description": "Fetches data from external sources",
        "inputSchema": {
          "type": "object",
          "properties": {
            "source": { "type": "string", "description": "Data source URL" },
            "limit": { "type": "number", "description": "Maximum records" }
          },
          "required": ["source"]
        }
      }
    ]
  }
}
```

## Tool Invocation

### Syntax

```comptext
use:fetch-data(source="https://api.example.com", limit=100)
```

### JSON-RPC Mapping

```json
{
  "jsonrpc": "2.0",
  "method": "tools/call",
  "id": 2,
  "params": {
    "name": "fetch-data",
    "arguments": {
      "source": "https://api.example.com",
      "limit": 100
    }
  }
}
```

## Tasks (Asynchronous Operations)

CompText DSL supports MCP's **Tasks primitive** for long-running operations introduced in the November 2025 specification.

### Task Definition

```comptext
task data-processing {
  name: "data-processing"
  description: "Processes large datasets asynchronously"
  timeout: 300000
  handler: "process_data"
}
```

### Task Lifecycle

1. **Creation**: Task is registered with the MCP server
2. **Execution**: Task runs asynchronously, returning a task handle
3. **Progress**: Server publishes progress updates via notifications
4. **Completion**: Results delivered when operation completes

### Task States

| State | Description |
|-------|-------------|
| `pending` | Task created, awaiting execution |
| `running` | Task is currently executing |
| `completed` | Task finished successfully |
| `failed` | Task encountered an error |
| `cancelled` | Task was cancelled |

### Progress Notifications

```json
{
  "jsonrpc": "2.0",
  "method": "tasks/progress",
  "params": {
    "taskId": "task-123",
    "progress": 0.45,
    "message": "Processing record 450 of 1000"
  }
}
```

## Resources

MCP Resources are referenced using the `@` prefix syntax.

### Syntax

```comptext
@database/users/schema
@config/settings
```

### Resource URI Mapping

| CompText | MCP Resource URI |
|----------|------------------|
| `@database/users` | `resource://database/users` |
| `@config/settings` | `resource://config/settings` |

## Authorization

CompText DSL supports MCP's OAuth 2.1 authorization framework.

### Protected Resource Metadata

```comptext
import "auth-config" as auth

auth.configure({
  authorization_endpoint: "https://auth.example.com/authorize",
  token_endpoint: "https://auth.example.com/token",
  resource_indicators: ["https://api.example.com"]
})
```

### Resource Indicators (RFC 8707)

Per MCP June 2025 updates, clients must implement Resource Indicators to prevent token mis-redemption.

## Server Capabilities

CompText DSL can declare MCP server capabilities:

```comptext
capabilities {
  tools: true
  resources: true
  prompts: true
  tasks: true
}
```

## Error Handling

### MCP Error Codes

| Code | Name | Description |
|------|------|-------------|
| -32700 | Parse Error | Invalid JSON |
| -32600 | Invalid Request | Invalid JSON-RPC |
| -32601 | Method Not Found | Unknown method |
| -32602 | Invalid Params | Invalid parameters |
| -32603 | Internal Error | Server error |

### CompText Error Mapping

```comptext
on_error(code=-32602) {
  log("Invalid parameters provided")
  retry(max=3, backoff="exponential")
}
```

## Integration Example

Complete example showing MCP integration:

```comptext
# Import MCP configuration
import "mcp-server" as mcp

# Define tools
tool analyze-code {
  name: "analyze-code"
  description: "Analyzes code for issues"
  inputSchema: {
    type: "object",
    properties: {
      file: { type: "string" },
      language: { type: "string" }
    },
    required: ["file"]
  }
}

# Define async task
task full-analysis {
  name: "full-analysis"
  timeout: 600000
  handler: "run_analysis"
}

# Use tools
use:analyze-code(file="src/main.py", language="python")

# Reference resources
@project/readme
@config/linting-rules
```

## See Also

- [MCP Specification](https://modelcontextprotocol.io/specification/2025-11-25)
- [Codex Skills Integration](codex-skills.md)
- [CompText Syntax](syntax.md)
