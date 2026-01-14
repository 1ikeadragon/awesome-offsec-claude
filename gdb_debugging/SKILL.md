---
name: GDB Debugging
description: Absolute control over process execution, memory inspection, and automated state analysis using GEF/Pwndbg.
author: 1ikeadragon
license: GPLv3
---

# GDB Debugging

## Skill Overview

**Prerequisites**: Linux Console Mastery, Python Scripting  
**Goal**: Absolute control over process execution, memory inspection, and automated state analysis.

---

## Core Tooling: GEF vs Pwndbg

The elite debugger relies on detailed context. Choose your weapon:

### GEF (GDB Enhanced Features)
*Best for: Lightweight analysis, broad architecture support (ARM/MIPS/PowerPC).*
- **Command**: `heap-analysis` (Glibc visualization).
- **Command**: `checksec` (Mitigation enumeration).

### Pwndbg
*Best for: Heap Exploitation, QEMU/GdbServer.*
- **Command**: `vis_heap_chunks` (Interactive layout), `telescope` (Stack analysis).

---

## Workflows

### 1. Exploit Development Pipeline
**Scenario**: Buffer overflow to ROP chain.
1. **Crash**: Catch SIGSEGV.
2. **Analysis**: Use `cyclic` to find offset.
3. **Gadgets**: Use `rop` command to find `pop rdi; ret`.
4. **Integration**: Feed findings into `binary_exploitation` scripts.

### 2. Heap Feng Shui Visualization
**Scenario**: UAF exploit layout.
1. **Baseline**: `vis_heap_chunks`.
2. **Action**: Trigger allocation/free in target.
3. **Trace**: Monitor tcache/bins via `context heap`.

---

## Advanced Scripting (Python)

Automate complex analysis using GDB's Python API.
- **Brute-Force De-ASLR**: Script GDB to run 1000x and analyze base address entropy.
- **Structure Parsing**: Write "Pretty Printers" for custom C++ structures.
- **Heap Tracer**: Hook `malloc`/`free` to log arguments (refer to `binary_reverser` for similar Frida hooks).

---

## Configuration Mastery
- `set follow-fork-mode parent`
- `set pagination off`
- `set disassembly-flavor intel`
