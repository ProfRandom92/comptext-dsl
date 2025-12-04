# CompText DSL Examples

## Basic Usage

### Single Command
```comptext
use:docker-setup-v1
```

### Multiple Commands
```comptext
use:project-init
use:git-workflow
use:ci-cd-setup
```

### With Modules
```comptext
module:development
use:coding-standards
use:test-framework
```

## Advanced Patterns

### Conditional Commands
```comptext
# Production deployment
use:prod-config
use:security-hardening
```

### Composable Patterns
```comptext
module:full-stack
use:frontend-setup
use:backend-api
use:database-schema
```
