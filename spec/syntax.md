# CompText DSL - Formal Syntax Specification

## Version 1.0.0

## Overview

CompText DSL is a token-efficient language designed for LLM interactions. This document defines the formal syntax and semantic rules.

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
command ::= identifier [ parameters ] ";"
identifier ::= (letter | "_") { letter | digit | "-" | "_" }
```

**Examples:**
```
fetch-data(source="api", limit=100);
transform(type="json");
store;
```

### 2. Parameters

```ebnf
parameters ::= "(" [ param-list ] ")"
param-list ::= param { "," param }
param ::= key "=" value
key ::= identifier
```

**Type System:**
- `string`: Text values in double quotes
-> `number`: Integer or floating-point
- `boolean`: `true` or `false`
- `array`: Ordered collection `[value, ...]`
- `object`: Key-value pairs `{key: value, ...}`

### 3. Module System

```ebnf
module-import ::= "import" string [ "as" identifier ] ";"
```

**Example:**
```
import "data-processing" as dp;
dp.clean(method="robust");
```

## Semantic Rules

### Scoping

1. **Global Scope**: Module imports and top-level commands
2. **Module Scope**: Identifiers within imported modules
3. **Parameter Scope**: Values within parameter lists

### Type Safety

- Strong typing with implicit conversions
- Type mismatches result in compilation errors
- Array and object types can be nested

### Execution Model

1. Sequential execution of commands
2. Module imports are resolved first
3. Commands execute in declaration order

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

## Best Practices

1. **Naming**: Use kebab-case for multi-word identifiers
2. **Parameters**: Always quote string values
3. **Modularity**: Group related commands into modules
4. **Comments**: Use `//` for single-line, `/* */` for multi-line

## Token Efficiency

CompText DS Xchieves **90-95% token reduction** compared to natural language prompts:

| Approach | Tokens | Reduction |
|----------|--------|-----------|
| Natural Language | 150-200 | - |
| CompText DSL | 10-15 | 92-95% |

## See Also

- [Language Tutorial](tutorial.md)
- [API Reference](api-reference.md)
- [Examples](examples.md)
