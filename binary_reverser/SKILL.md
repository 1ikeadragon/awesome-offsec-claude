---
name: Binary Reverser
description: Deconstruct complex binaries (ELF/PE/Mach-O/APK) to identify hidden logic, bypass protections, and discover vulnerabilities.
author: 1ikeadragon
license: GPLv3
---

# Binary Reverser

## Skill Overview

**Prerequisites**: Assembly (x86/ARM), C/C++, OS Internals  
**Goal**: Deconstruct complex binaries to identify hidden logic, bypass protections, and discover vulnerabilities.

---

## Core Principles

### Hybrid Analysis Paradigm
The elite reverser fuses static and dynamic analysis.
- **Static**: Ghidra/IDA/Binary Ninja.
- **Dynamic**: Frida/GDB/LLDB.

---

## Tool Mastery

### Ghidra: Advanced Static Analysis
*Focus on Ghidra 11.x+ features*

- **Scripting**: Use Java/Python API to automate renaming and structure application.
- **P-Code Emulation**: Emulate snippets to resolve calculated jumps or decrypt strings.
- **Headless**: Batch analysis for patch diffing.

### Frida: Dynamic Instrumentation
*Runtime manipulation of process memory*

- **Interceptor**: Hook `open`, `read`, `write`, `memcmp` to trace data.
- **Stalker**: Code tracing for coverage.
- **Integration**: Use `ios_ipsw_analysis` skill findings to target specific proprietary dylibs.

---

## Workflows

### 1. Malware Analysis & De-obfuscation
**Scenario**: Android banking trojan with encrypted payload.
1. **Static**: Identify JNI methods in Ghidra.
2. **Dynamic**: Use Frida to hook `JNI_OnLoad` and scan memory for "DEX" headers.
3. **Dump**: Extract the decrypted payload for further static analysis.

### 2. Vulnerability Discovery (Binary Diffing)
**Scenario**: Patch analysis (1-day).
1. Diff `v1` vs `v2`.
2. Locate changes (added checks).
3. Verify reachability using `gdb_debugging` skill.

### 3. Protocol Reverse Engineering
**Scenario**: Custom C2 protocol.
1. Trace `recv()` buffers.
2. Hook crypto functions (AES/RSA) to dump keys.

---

## Advanced Techniques

### Symbolic Execution (Angr)
- Automate path discovery for crackmes or complex constraints.

### Slicing
- Isolate instructions affecting a specific register to reduce noise (Ghidra Slice plugin).

---

## Case Studies
- **Android Packer Unpacking**: Manual unpacking via GDB/Frida.
- **iOS Jailbreak Detection**: Hooking file system checks (`lstat`, `access`).
