---
name: iOS IPSW Analysis
description: Analyze iOS firmware, extract kernels, and audit Apple's proprietary drivers without a physical device.
author: 1ikeadragon
license: GPLv3
---

# iOS IPSW Analysis

## Skill Overview

**Prerequisites**: macOS/Linux CLI, ARM64 Assembly, Mach-O Format  
**Goal**: Analyze iOS firmware, extract kernels, and audit Apple's proprietary drivers.

---

## Core Tooling: ipsw
The swiss army knife for iOS research (by blacktop).
- **Download**: Signed/unsigned firmwares.
- **Extract**: Kernelcache, dyld_shared_cache, KEXTs.
- **Symbolicate**: Restore stripped symbols.

---

## Workflows

### 1. Kernel Cache Analysis
**Scenario**: Auditing IOKit drivers.
1. **Fetch**: `ipsw download --device iPhone14,7`.
2. **Extract & Decrypt**: `ipsw extract --kernel` -> `ipsw kernel dec`.
3. **Symbolicate**: `ipsw kernel symbolicate`.
4. **Analyze**: Load into Ghidra (use `ghidra_kernelcache` loader).

### 2. Filesystem & App Analysis
**Scenario**: Finding logic bugs in system daemons.
1. **Mount**: `ipsw mount payload.ipsw`.
2. **Split Cache**: `ipsw dyld split` to get individual dylibs.
3. **Audit**: Analyze binaries in `/usr/libexec/` using `binary_reverser` skills.

---

## Jailbreak Internals
- **Rootless (Dopamine/Palera1n)**: Tweaks in `/var/jb`, Procursus bootstrap.
- **Bootrom (checkm8)**: Unpatchable hardware bug (A5-A11).

---

## Advanced Techniques
- **Diffing**: `ipsw diff kernel.old kernel.new` to find silent patches.
- **XPC Fuzzing**: Map XPC message structures statically to guide fuzzing.
