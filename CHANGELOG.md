# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.1.0] - 2026-01-29

### Added

#### MCP Integration (Model Context Protocol)
- Full compatibility with [MCP November 2025 Specification](https://modelcontextprotocol.io/specification/2025-11-25)
- `tool` keyword for defining MCP-compatible tools with JSON Schema input
- `task` keyword for async operations (MCP Tasks primitive)
- `@resource` syntax for MCP resource references
- JSON-RPC 2.0 protocol message definitions in grammar
- OAuth 2.1 authorization framework support
- Resource Indicators (RFC 8707) compliance

#### Codex Skills Integration
- `$skill-name` syntax for [OpenAI Codex Agent Skills](https://developers.openai.com/codex/skills/) invocation
- Skill definition blocks for generating SKILL.md files
- Skill composition and pipeline support
- AGENTS.md configuration generation
- Parameter schemas for skills

#### New Specifications
- `spec/mcp-integration.md` - Complete MCP integration guide
- `spec/codex-skills.md` - Codex Skills specification

### Changed
- Updated `grammar.ebnf` to v1.1 with new constructs
- Expanded `docs/ARCHITECTURE.md` with integration diagrams
- Enhanced `spec/syntax.md` with new syntax rules
- Updated `README.md` with integration examples

### Compatibility
- Full backward compatibility with v1.0 syntax
- Legacy `use:command` syntax remains supported
- Optional `legacy-use-syntax` config flag

---

## [1.0.0] - 2025-12-04

### Added
- Initial DSL specification
- Core grammar definition
- Documentation structure
- Examples and tutorials

### Components
- Language specification
- Parser design
- Compiler architecture
- Runtime integration
