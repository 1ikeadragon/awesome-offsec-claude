---
name: Cloud Security
description: Exploitation of cloud-native services (AWS/Azure/GCP), IAM privilege escalation, and metadata abuse.
author: 1ikeadragon
license: GPLv3
---

# Cloud Security

## Skill Overview

**Prerequisites**: IAM concepts, JSON/YAML, CI/CD pipelines  
**Goal**: Escalate privileges from a compromised instance/container to full cloud account takeover.

---

## Core Principles

### The Metadata Service (169.254.169.254)
The holy grail of cloud exploitation.
- **AWS**: `http://169.254.169.254/latest/meta-data/iam/security-credentials/` (IMDSv1 is vulnerable to SSRF).
- **Azure**: Requires `Metadata: true` header.
- **GCP**: Requires `Metadata-Flavor: Google` header.

### IAM Privilege Escalation
Misconfigured permissions allow indirect admin access.
- **PassRole**: The ability to give a role to a resource (EC2/Lambda) is effectively the ability to *be* that role.

---

## Workflows

### 1. AWS IAM Escalation (`iam:PassRole`)
**Scenario**: You have compromised a developer's access keys with `iam:PassRole` and `ec2:RunInstances`.
1. **Enumeration**: `aws iam list-roles` to find a highly privileged role (e.g., `AdminRole`).
2. **Exploit**: Launch a new EC2 instance, passing the `AdminRole` to it.
3. **Access**: SSH into the new instance. It now possesses the `AdminRole` credentials in its metadata.

### 2. Azure Managed Identity Abuse
**Scenario**: SSRF on an App Service.
1. **Trigger**: SSRF to `http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/`.
2. **Bypass**: If the SSRF allows setting headers, add `Metadata: true`.
3. **Use**: Use the returned JWT to query the Azure Resource Manager API.

---

## Advanced Techniques

### GCP Service Account Impersonation
- **Permissions**: `iam.serviceAccounts.actAs`.
- **Attack**: Generate a short-lived access token for a target service account.
- **Command**: `gcloud auth print-access-token --impersonate-service-account=target@project.iam.gserviceaccount.com`.

### AWS Lambda Persistence
- **Backdoor**: Add a malicious "Layer" to all existing Lambda functions.
- **Impact**: Every time the function triggers, your code runs (stealing event data/env vars).

---

## Case Studies
- **Capital One Breach**: SSRF to AWS metadata service (IMDSv1).
- **Azure OMIGOD**: RCE in logic agents allowing identity theft.
