# CompText DSL Specification v1.0

## Overview

CompText DSL is a Domain-Specific Language designed for token-efficient LLM interactions.

## Syntax

### Command Reference
```
use:command-id
```

### Module Reference
```
module:module-name
```

### Inline Commands
```
[command-id]
```

## Grammar

```ebnf
program ::= statement+
statement ::= command | module | comment
command ::= "use:" identifier
module ::= "module:" identifier  
identifier ::= [a-zA-Z0-9-_]+
comment ::= "#" text
```

## Examples

See [EXAMPLES.md](../EXAMPLES.md) for full examples.
