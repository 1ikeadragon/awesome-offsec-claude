---
name: WAF Bypass Hunter
description: Elite techniques to identify and exploit parser differentials for WAF evasion.
author: 1ikeadragon
license: GPLv3
---

# WAF Bypass Hunter

## Skill Overview

**Prerequisites**: HTTP protocol, parser differentials, encoding techniques  
**Goal**: Identify and exploit parser differentials between WAF and backend to bypass security controls

---

## Core Principles

### Understanding Parser Differentials

WAFs and backends parse requests differently. Exploit these gaps:

- **Encoding inconsistencies** - WAF normalizes differently than backend
- **Boundary handling** - Multipart parsing differences
- **Character interpretation** - Unicode, whitespace, null bytes
- **Protocol quirks** - HTTP header parsing edge cases
- **Format validation** - Strict vs lenient parsers

### The Attack Flow
1. **Recon**: Identify WAF technology (see `advanced_recon` skill).
2. **Analyze**: Map blocked patterns.
3. **Study**: WAF vs Backend parser implementation.
4. **Differential**: Find behavioral gaps.
5. **Exploit**: Craft bypass payload.

---

## Reconnaissance Phase

### Identify WAF Technology
```bash
# Header analysis
curl -I https://target.com | grep -iE "server|x-waf|cf-ray"

# Error page fingerprinting
curl https://target.com/';DROP%20TABLE --
```

### Map Blocked Patterns
```python
patterns = ["proto", "constructor", "../", "<?php"]
# Iterate and check for 403s
```

---

## Bypass Techniques

### Technique 1: Encoding Bypasses

**Case Variations**
- `proto`  → `PROTO`

**URL Encoding**
- `constructor` → `con%73tructor`

**Double Encoding**
- `../` → `%252e%252e%252f`

**Unicode Normalization**
- `<` → `\u003c`

### Technique 2: Multipart Boundary Manipulation

**Boundary Confusion**
```http
Content-Type: multipart/form-data; boundary=yyy--yyy
Content-Disposition: form-data; name="field"

payload
--yyy--
```
*Variations*: Missing CR, trailing space, space in boundary.

---

## Practical Workflow

### Step 1: Baseline Testing
Ensure a vanilla malicious request is blocked (403).

### Step 2: Systematic Bypass Testing
Use `Burp Suite` Intruder or Python scripts to fuzz encodings and header variations.

### Step 3: Flag Extraction
Monitor responses for 200 OK and specific headers indicating success.

---

## Advanced Techniques

### Technique 6: Protocol Smuggling
- **HTTP/Main**: CL.TE and TE.CL smuggling to hide the payload from the WAF but have it processed by the backend.

### Technique 8: Polyglot Payloads
- **Polyglots**: Payloads that are valid in multiple contexts (JSON/JS) to confuse parsers.

---

## References
- HTTP/1.1 RFC 7230
- Multipart RFC 2046
