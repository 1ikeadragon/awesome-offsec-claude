---
name: Secure Code Review
description: Master static analysis, threat modeling, and finding logical vulnerabilities in source code.
author: 1ikeadragon
license: GPLv3
---

# Secure Code Review

## Skill Overview

**Prerequisites**: Fluency in Java/Python/Go/JS, CI/CD knowledge  
**Goal**: Identify critical vulnerabilities (AuthZ, Logic bugs) that scanners miss, using advanced static analysis and manual review.

---

## Core Principles

### Threat Modeling First (PASTA/STRIDE)
Don't just read code; model the attack surface.
- **PASTA**: Risk-centric. align technical findings with business impact.
- **Data Flow Analysis**: Trace "tainted" user input from Source (API controller) to Sink (Database/Eval).

---

## Tool Mastery

### Semgrep: The Scalable Hunter
*Forget simple greps. Use semantic awareness.*

- **Custom Rules**: Write rules for company-specific logic bugs.
    ```yaml
    rules:
      - id: missing-internal-auth
        patterns:
          - pattern: |
              @Controller
              class $CLASS {
                ...
                @RequestMapping(...)
                public $RET $METHOD(...) { ... }
              }
          - pattern-not-inside: |
              @PreAuthorize("hasRole('INTERNAL')")
              class $CLASS { ... }
        message: "Internal controller missing @PreAuthorize annotation"
        languages: [java]
    ```

### CodeQL
- **Querying**: Use CodeQL for deep inter-procedural data flow analysis that spans multiple files.

---

## Workflows

### 1. The Authorization Audit
**Scenario**: Bypassing tenant isolation in a SaaS.
1. **Identify Pattern**: Find all database accessors (`Repository.findById`).
2. **Verify Context**: Check if `tenant_id` is applied in the WHERE clause.
3. **Automate**: Write a Semgrep rule to flag any DB query missing `tenant_id` filter (using `pattern-not`).

### 2. Deserialization Hunting
**Scenario**: Java/Python unsafe deserialization.
1. **Sources**: `readObject`, `pickle.load`, `yaml.load`.
2. **Gadgets**: Look for `InvocationHandler`, `exec` calls in available libraries (use `ysoserial` knowledge).
3. **Exploit**: Verify if user input reaches these sinks without validation.

---

## Language-Specific Checklists

### Java (Spring Boot)
- **Mass Assignment**: Check `@RequestBody` mapping to entities without DTOs.
- **SpEL Injection**: Search for `ExpressionParser.parseExpression` with user input.

### Go
- **Concurrency**: Race conditions in `go routines` sharing map access.
- **Unsafe**: Usage of `unsafe.Pointer` or `reflect` package bypasses type safety.

### Python (Django/Flask)
- **SSTI**: Jinja2 templates rendering user input (`{{ input }}`).
- **Pickle**: Never trust `pickle` data from network.

---

## Advanced Techniques
- **Variant Analysis**: Once a bug is found, use Semgrep to find *all* other occurrences of that pattern across the entire organization's codebase.
- **Diff Analysis**: Review code changes (PRs) specifically for security regressions using `git diff` heuristic tools.
