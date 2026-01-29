# CompText DSL

**Token-efficient Domain-Specific Language for LLM interactions**

[![MCP Compatible](https://img.shields.io/badge/MCP-2025--11--25-blue)](https://modelcontextprotocol.io)
[![Codex Skills](https://img.shields.io/badge/Codex-Skills-green)](https://developers.openai.com/codex/skills/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

## What is CompText DSL?

CompText DSL is a specialized language designed for efficient communication with Large Language Models. Instead of sending massive context repeatedly, you define structured, reusable commands that integrate seamlessly with modern AI tooling.

## Key Benefits

- **90-95% token reduction** - Dramatically reduce API costs
- **MCP Compatible** - Full [Model Context Protocol](https://modelcontextprotocol.io) support (November 2025 spec)
- **Codex Skills** - Native [OpenAI Codex CLI](https://developers.openai.com/codex/cli/) integration
- **Standardized patterns** - Consistent, reusable commands
- **Version controlled** - Track changes to your AI interactions
- **Team collaboration** - Share commands across your organization

## Quick Examples

### Traditional vs CompText

Instead of:
```
"Here's my entire coding style guide... [25,000 tokens]"
```

Use:
```comptext
use:code-style-v2
```

### Codex Skills (v1.1)

```comptext
# Invoke Codex skills directly
$code-review
$draft-commit-message(style="conventional")
$analyze-tests
```

### MCP Tools (v1.1)

```comptext
# Define MCP-compatible tools
tool fetch-data {
  name: "fetch-data"
  description: "Fetches data from external sources"
  inputSchema: {
    type: "object",
    properties: {
      source: { type: "string" },
      limit: { type: "number" }
    }
  }
}

# Use MCP resources
@database/users/schema
@config/settings
```

### Async Tasks (v1.1)

```comptext
# Define long-running operations
task data-processing {
  name: "data-processing"
  timeout: 300000
  handler: "process_data"
}
```

## Integration

CompText DSL works with:

| Platform | Protocol | Status |
|----------|----------|--------|
| [Claude](https://claude.ai) | MCP | Full Support |
| [Codex CLI](https://developers.openai.com/codex/cli/) | Skills | Full Support |
| [ChatGPT](https://chat.openai.com) | Skills/MCP | Full Support |
| [Claude Code](https://docs.anthropic.com/claude-code) | MCP | Full Support |

### Related Projects

- [CompText MCP Server](https://github.com/ProfRandom92/comptext-mcp-server) - Universal MCP integration
- [CompText Docs](https://github.com/ProfRandom92/comptext-docs) - Full documentation
- Public Codex - 150+ ready commands

## Getting Started

```bash
# Coming soon: parser installation
pip install comptext-dsl
```

### Basic Usage

```comptext
# Import modules
import "data-processing" as dp

# Define variables
api_url := "https://api.example.com"

# Use commands
dp.fetch(source=$api_url)
dp.transform(type="json")

# Invoke skills
$validate-output
```

## Documentation

- [Syntax Specification](spec/syntax.md)
- [MCP Integration](spec/mcp-integration.md)
- [Codex Skills](spec/codex-skills.md)
- [Architecture](docs/ARCHITECTURE.md)
- [Examples](EXAMPLES.md)
- [Changelog](CHANGELOG.md)

## Protocol Compatibility

### MCP (Model Context Protocol)

CompText DSL v1.1 is fully compatible with the [MCP November 2025 Specification](https://modelcontextprotocol.io/specification/2025-11-25):

- JSON-RPC 2.0 protocol
- Tool definitions and invocations
- Resource access with `@` syntax
- Async Tasks primitive
- OAuth 2.1 authorization

### Codex Skills

CompText DSL v1.1 supports [OpenAI Codex Agent Skills](https://developers.openai.com/codex/skills/):

- `$skill-name` invocation syntax
- SKILL.md generation
- Skill composition and pipelines
- AGENTS.md integration

## Version History

| Version | Date | Highlights |
|---------|------|------------|
| v1.1.0 | Jan 2026 | MCP Nov 2025 & Codex Skills alignment |
| v1.0.0 | Dec 2025 | Initial release |

## Contributing

Contributions are welcome! Please see our [Contributing Guidelines](CONTRIBUTING.md) and [Code of Conduct](CODE_OF_CONDUCT.md).

## Security

For security issues, please see our [Security Policy](SECURITY.md).

## License

MIT License - see [LICENSE](LICENSE)

## Citation

If you use CompText DSL in your research, please cite:

```bibtex
@software{comptext-dsl,
  title = {CompText DSL},
  author = {ProfRandom92},
  year = {2025},
  url = {https://github.com/ProfRandom92/comptext-dsl}
}
```

---

**Resources:**
- [MCP Specification](https://modelcontextprotocol.io/specification/2025-11-25)
- [Codex CLI Documentation](https://developers.openai.com/codex/cli/)
- [Codex Skills Documentation](https://developers.openai.com/codex/skills/)
