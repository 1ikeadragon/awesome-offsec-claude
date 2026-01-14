---
name: Web Application Security
description: Advanced exploitation of modern web architectures (JWT, GraphQL, HTTP/2).
author: 1ikeadragon
license: GPLv3
---

# Web Application Security

## Skill Overview

**Prerequisites**: HTTP Expert, Crypto basics, API design patterns  
**Goal**: Exploit architectural flaws in modern web stacks beyond OWASP Top 10.

---

## Core Principles

### Stateless Authentication Attacks (JWT)
The server trusts the signature. You break the trust.
- **Algorithm Confusion**: Force `RS256` -> `HS256` using the public key as the secret.
- **None Algorithm**: Set `alg: none` and strip signature.
- **Key Injection**: If `jwks_uri` is unchecked, point it to your own server.

### API Graph Theory (GraphQL)
APIs are no longer flat endpoints; they are graphs.
- **Introspection**: The map of the kingdom. Always try `__schema` query.
- **Complexity Attacks**: Nested queries (`author { posts { author { posts... } } }`) to cause DoS.
- **Batching**: Bypass rate limits by stacking multiple queries in one request.

---

## Workflows

### 1. Advanced JWT Exploitation
**Scenario**: Admin account takeover via JWT.
1. **recon**: Decode token (`jwt.io`). Identify `kid` and `alg`.
2. **attack**: Use `jwt_tool` to fuzz known secrets or try "None" algorithm.
3. **forge**: If public key is leaked, try algorithm confusion (`HS256` signed with pubkey).

### 2. HTTP Request Smuggling (CL.TE / TE.CL)
**Scenario**: Bypassing WAF or poisoning cache.
1. **Detect**: Send ambiguous requests (`Content-Length` != `Transfer-Encoding` body size).
2. **Confirm**: Timeouts or 400 Bad Request on subsequent requests.
3. **Exploit**: Smuggle a generic admin request (`POST /admin`) attached to the next user's packet.

---

## Advanced Techniques

### GraphQL Injection
- **Fragment Injection**: Use fragments to bypass depth checks or WAF signatures.
- **Alias Overloading**: Request 1000 fields using aliases to brute-force permissions.

### Race Conditions
- **Turbo Intruder**: Use HTTP/2 single-packet attack to send parallel requests.
- **Limit Bypass**: Use race conditions on "Check then Act" logic (e.g., couypon redemption).

---

## Case Studies
- **GitLab GraphQL**: Information disclosure via aliasing.
- **F5 BIG-IP**: Request Smuggling allowing authentication bypass.
