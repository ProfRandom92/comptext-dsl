# CompText DSL - Codex Skills Specification

## Version 1.1.0

## Overview

This document specifies how CompText DSL integrates with **OpenAI Codex CLI Agent Skills** as introduced in January 2026.

## Codex Skills Compatibility

CompText DSL supports the Codex Skills format, enabling seamless integration with the Codex CLI and IDE extensions.

### References

- [Codex Agent Skills](https://developers.openai.com/codex/skills/)
- [Create Skills](https://developers.openai.com/codex/skills/create-skill/)

## Skill Invocation Syntax

CompText DSL uses the `$` prefix for skill invocation, matching Codex CLI's native syntax.

### Basic Invocation

```comptext
# Invoke a skill by name
$draft-commit-message

# Invoke with parameters
$code-review(file="src/main.py", style="strict")
```

### Skill Resolution

Skills are resolved in the following order:

1. **Repo-scoped**: `.codex/skills/<skill-name>/SKILL.md`
2. **User-scoped**: `~/.codex/skills/<skill-name>/SKILL.md`
3. **CompText Codex**: Remote skill repository

## Skill Definition Format

CompText DSL can generate Codex-compatible SKILL.md files.

### YAML Front Matter

```comptext
skill code-review {
  name: "code-review"
  description: "Reviews code for best practices and potential issues"
  metadata: {
    short-description: "Review code quality"
  }
}
```

Generates:

```markdown
---
name: code-review
description: Reviews code for best practices and potential issues
metadata:
  short-description: Review code quality
---
```

### Full Skill Definition

```comptext
skill analyze-tests {
  name: "analyze-tests"
  description: "Analyzes test coverage and suggests improvements when asked about testing"
  metadata: {
    short-description: "Analyze test coverage"
    category: "testing"
    version: "1.0.0"
  }

  # Skill instructions (body content)
  instructions: """
    Analyze the test files in the repository.

    1. Check test coverage metrics
    2. Identify untested code paths
    3. Suggest additional test cases
    4. Review test quality and naming

    Output a structured report with recommendations.
  """

  # Associated resources
  resources: [
    "templates/test-report.md",
    "schemas/coverage.json"
  ]

  # Associated scripts
  scripts: [
    "scripts/coverage.py",
    "scripts/analyze.sh"
  ]
}
```

## Skill Directory Structure

CompText DSL generates the following structure:

```
.codex/skills/
└── analyze-tests/
    ├── SKILL.md           # Main skill definition
    ├── templates/
    │   └── test-report.md # Report template
    ├── schemas/
    │   └── coverage.json  # JSON schema
    └── scripts/
        ├── coverage.py    # Coverage script
        └── analyze.sh     # Analysis script
```

## Automatic Skill Selection

Codex can automatically select skills based on user prompts. CompText DSL supports skill matching hints:

```comptext
skill fix-linting {
  name: "fix-linting"
  description: "Fixes linting errors when the user mentions lint, eslint, or code style issues"

  # Matching patterns for auto-selection
  triggers: [
    "lint",
    "eslint",
    "code style",
    "formatting"
  ]
}
```

## Skill Composition

CompText DSL supports composing multiple skills:

```comptext
# Sequential skill execution
$analyze-code
$fix-issues
$run-tests

# Skill pipeline
pipeline code-quality {
  steps: [
    $lint-check,
    $type-check,
    $security-scan,
    $test-coverage
  ]
}
```

## Configuration

### Per-Skill Configuration

CompText DSL can generate Codex config entries:

```comptext
skill-config {
  path: "/path/to/skill"
  enabled: true
}
```

Generates in `~/.codex/config.toml`:

```toml
[[skills.config]]
path = "/path/to/skill"
enabled = true
```

### Skill Parameters

```comptext
skill generate-docs {
  name: "generate-docs"
  description: "Generates documentation for code"

  parameters: {
    format: {
      type: "string",
      enum: ["markdown", "html", "rst"],
      default: "markdown"
    },
    include-examples: {
      type: "boolean",
      default: true
    }
  }
}
```

## MCP Integration

Codex Skills can leverage MCP tools. CompText DSL bridges both:

```comptext
# Define MCP tool
tool read-file {
  name: "read-file"
  description: "Reads file contents"
  inputSchema: {
    type: "object",
    properties: {
      path: { type: "string" }
    },
    required: ["path"]
  }
}

# Skill using MCP tool
skill analyze-file {
  name: "analyze-file"
  description: "Analyzes a file using MCP tools"

  instructions: """
    Use the read-file MCP tool to read the file contents,
    then analyze and provide insights.
  """

  mcp-tools: ["read-file"]
}
```

## AGENTS.md Integration

CompText DSL supports Codex's `AGENTS.md` custom instructions:

```comptext
agents-config {
  # Project-level instructions
  instructions: """
    This project uses TypeScript with strict mode.
    Follow the coding standards in docs/STYLE.md.
    Always run tests before committing.
  """

  # Preferred tools
  tools: ["npm", "vitest", "eslint"]

  # File patterns to focus on
  focus: ["src/**/*.ts", "tests/**/*.test.ts"]
}
```

## Migration from CompText Commands

Existing CompText commands can be migrated to Codex Skills:

### Before (CompText v1.0)

```comptext
use:code-style-v2
use:test-framework
```

### After (CompText v1.1 with Skills)

```comptext
$code-style
$test-framework
```

### Compatibility Mode

```comptext
# Enable backward compatibility
config {
  legacy-use-syntax: true
}

# Both syntaxes work
use:old-command    # Legacy
$new-skill         # Modern
```

## Examples

### Complete Skill: Draft PR Description

```comptext
skill draft-pr-description {
  name: "draft-pr-description"
  description: "Drafts a pull request description based on git diff and commit history"
  metadata: {
    short-description: "Draft PR description"
    category: "git"
  }

  instructions: """
    Generate a comprehensive PR description:

    1. Summarize the changes from git diff
    2. List the commits included
    3. Identify breaking changes
    4. Suggest reviewers based on CODEOWNERS

    Format the output as:
    ## Summary
    [Brief description]

    ## Changes
    - [Change 1]
    - [Change 2]

    ## Test Plan
    [How to test]
  """

  scripts: ["scripts/get-diff.sh"]
}
```

### Skill with MCP Resources

```comptext
skill apply-template {
  name: "apply-template"
  description: "Applies a project template"

  # Use MCP resources
  resources: [
    @templates/react-component,
    @templates/test-file
  ]

  parameters: {
    template: { type: "string", required: true },
    name: { type: "string", required: true }
  }
}
```

## See Also

- [MCP Integration](mcp-integration.md)
- [CompText Syntax](syntax.md)
- [Codex CLI Documentation](https://developers.openai.com/codex/cli/)
