---
name: JavaScript Analysis
description: Master client-side analysis, deobfuscation, and DOM vulnerability discovery.
author: 1ikeadragon
license: GPLv3
---

# JavaScript Analysis

## Skill Overview

**Prerequisites**: JavaScript/TypeScript, Browser DevTools, AST Parsing  
**Goal**: Uncover client-side logic flaws, DOM XSS, and hidden API endpoints.

---

## Core Principles

### The Client-Side Trust Boundary
Never trust the client.
- **Source Maps**: The map to the treasure (original code).
- **DOM Sources & Sinks**: The flow of taint from input (`location.hash`) to execution (`innerHTML`).

---

## Static Analysis (AST & Regex)

### Automated Scanners
- **ESLint Security Plugins**: Custom rules for dangerous patterns.
- **Semgrep**:
    ```yaml
    rules:
      - id: hazardous-sink
        pattern: element.innerHTML = $X
        message: "Potential DOM XSS"
        languages: [javascript]
    ```

### DOM XSS Detection
**Tools**: `Burp Suite DOM Invader`, `fin-dom-xss-scanner`.
1. **Identify Sources**: `location`, `document.referrer`, `window.name`.
2. **Identify Sinks**: `eval`, `setTimeout`, `document.write`.
3. **Trace**: Follow the data flow manually or using dynamic taint analysis.

---

## Deobfuscation & Reconstruction

### Source Map Magic
If `.map` files exist (check `advanced_recon` results):
- **Tool**: `sourcemapper` or `restore-source-tree`.
- **Action**: Reconstruct the full original source tree, including comments and variable names.

### Manual Deobfuscation
**Tool**: `JSNice`, `de4js`.
- **Technique**: Rename variables based on context (e.g., if it calls `express()`, name it `app`).
- **AST Manipulation**: Use `babel` scripts to remove dead code and resolve constant expressions.

---

## Workflows

### 1. The "Hidden Route" Hunt
**Scenario**: SPA (React/Vue) with client-side routing.
1. **Extract**: Run `LinkFinder` or `secretfinder.py` on main bundle.
2. **Regex**: `/(["'])(?:\/[a-zA-Z0-9_?=&/\-#]+)\1/g`.
3. **Verify**: Test found endpoints with `advanced_recon` findings.

### 2. DOM XSS Exploitation
**Scenario**: `location.hash` parameter flow.
1. **Sink**: `$('#content').html(decodedHash)`.
2. **Payload**: `https://target.com/#<img src=x onerror=alert(1)>`.
3. **Bypass**: If filtered, look for encoding differentials (refer to `waf_bypass`).

---

## Advanced Techniques

### Prototype Pollution
- **Concept**: Modifying `Object.prototype` to affect all objects.
- **Pattern**: `merge(target, source)` functions without key validation (`__proto__`).
- **Impact**: DoS, gadget injection, or XSS properly.

### Framework-Specific Analysis
- **React**: Look for `dangerouslySetInnerHTML`.
- **Angular**: Check for `bypassSecurityTrustHtml`.
- **Vue**: Inspect `v-html` directives.
