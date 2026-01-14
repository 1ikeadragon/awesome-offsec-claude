---
name: Mobile Application Security
description: Runtime instrumentation and deep analysis of Android/iOS apps.
author: 1ikeadragon
license: GPLv3
---

# Mobile Application Security

## Skill Overview

**Prerequisites**: Java/Kotlin (Android), Swift/Obj-C (iOS), ARM Assembly  
**Goal**: Bypass client-side controls (Root/SSL) and exploit IPC mechanisms.

---

## Core Tooling: Frida
Dynamic Binary Instrumentation (DBI) is the king.

### SSL Pinning Bypass
**Concept**: Hook the `SSLContext` or `TrustManager` verify methods to always return true.
- **Android**: Hook `okhttp3.CertificatePinner.check`.
- **iOS**: Hook `SecTrustEvaluate` or `boringssl_context_set_verify_mode`.
- **Universal Script**: Use `frida-scripts/universal-ssl-pinning-bypass.js`.

### Root/Jailbreak Detection Bypass
**Concept**: Hook file system checks or package manager queries.
- **Target**: `/system/bin/su`, `/bin/bash`, `Cydia.app`.
- **Action**: Return `false` or `null` when app checks for these files.

---

## Workflows

### 1. Deep Link Exploitation
**Scenario**: Account takeover via exported Activity.
1. **Manifest Analysis**: Find `<intent-filter>` with `android:scheme` (no `autoVerify`).
2. **Fuzzing**: Send intents via `adb shell am start -d "app://token=..."`.
3. **Impact**: If `WebView` loads the param, escalate to XSS -> RCE.

### 2. Hardcoded Secrets in Native Libraries
**Scenario**: API Keys hidden in `.so` files.
1. **Extract**: Unzip APK -> `lib/arm64-v8a/libnative.so`.
2. **Analyze**: Use `binary_reverser` skill (Ghidra) or `strings`.
3. **Hook**: Use Frida `Interceptor` to hook the JNI function returning the string.

---

## Advanced Techniques

### iOS Biometric Bypass
- Hook `LAContext.evaluatePolicy` to return `true`.

### Android Webview vulnerabilities
- **JavascriptInterface**: If `addJavascriptInterface` is used on API < 17 (rare) or indiscriminately, RCE is possible.
- **File Access**: Check `setAllowFileAccess(true)` allowing exfiltration of local files via XSS.
