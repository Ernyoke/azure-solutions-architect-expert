# Microsoft Defender for Cloud

- Microsoft Defender for Cloud (formerly **Azure Security Center** + **Azure Defender**) is a **Cloud-Native Application Protection Platform (CNAPP)**
- It provides three core capabilities:
    - **CSPM** (Cloud Security Posture Management): assess, harden, and continuously monitor the security posture of resources
    - **CWPP** (Cloud Workload Protection Platform): threat detection and protection for specific workloads (servers, databases, storage, containers, etc.)
    - **DevSecOps**: security from code to cloud (scanning IaC, container images, pipelines)
- Works across **Azure, on-premises, and other clouds (AWS, GCP)** via Azure Arc

## Two Main Pillars

### 1. CSPM — Security Posture Management

- **Foundational CSPM (free)**: enabled by default on all Azure subscriptions
    - **Secure Score**: a measurement (percentage) of the current security posture; higher is better
    - **Security recommendations**: actionable hardening steps mapped to Secure Score controls
    - Asset inventory and basic compliance visibility
- **Defender CSPM (paid plan)**: advanced posture management
    - **Attack path analysis** and **cloud security graph** to find exploitable risks
    - **Agentless vulnerability scanning** for machines and container registries
    - Data-aware security posture (sensitive data discovery)
    - Governance rules to drive remediation accountability

### 2. CWPP — Workload Protection (Defender Plans)

- Enabling a **Defender plan** turns on advanced threat detection for that resource type (per-resource, per-hour billing)
- Key plans:
    - **Defender for Servers**: VMs/servers (Azure, on-prem, multicloud via Arc)
        - Plan 1: core EDR via **Microsoft Defender for Endpoint (MDE)** integration
        - Plan 2: adds **JIT VM access**, **File Integrity Monitoring (FIM)**, agentless vulnerability assessment, adaptive application controls, free 500 MB/day log ingestion
    - **Defender for App Service**
    - **Defender for Storage**: detects malware uploads, suspicious access, malicious activity
    - **Defender for SQL** / Databases (Azure SQL, SQL on VMs, open-source DBs, Cosmos DB)
    - **Defender for Containers**: AKS and container registries (image scanning, runtime threat detection)
    - **Defender for Key Vault**: detects unusual access to secrets/keys
    - **Defender for Resource Manager**: monitors control-plane (management) operations
    - **Defender for DNS**
    - **Defender for APIs** (Azure API Management)

## Key Features

- **Secure Score**: prioritized, weighted recommendations; aim to increase the score
- **Regulatory Compliance dashboard**: maps controls to standards (Microsoft Cloud Security Benchmark by default, plus PCI-DSS, ISO 27001, NIST, CIS, etc.)
- **Security Alerts**: generated when threats are detected; can be aggregated into **incidents**
- **Workbooks**: visual interactive reports
- **Workflow automation**: trigger **Logic Apps** in response to alerts/recommendations (e.g. notify, remediate)
- **Just-in-Time (JIT) VM Access**: open management ports (RDP/SSH) only on request, time-bound, from approved IPs (requires Defender for Servers)
- **Adaptive Application Controls** and **Adaptive Network Hardening**
- **File Integrity Monitoring (FIM)**

## Architecture & Data Collection

- Requires a **Log Analytics workspace** to store collected security data
- Uses the **Azure Monitor Agent (AMA)** / Defender extensions to collect data from machines
- Onboard non-Azure machines (on-prem, AWS, GCP) using **Azure Arc**
- Multicloud connectors integrate AWS and GCP accounts natively

## Microsoft Cloud Security Benchmark (MCSB)

- The default compliance/policy initiative applied by Defender for Cloud
- A set of security best practices and controls aligned with industry frameworks
- Drives the default recommendations that feed into Secure Score

## Defender for Cloud vs Microsoft Sentinel

- **Defender for Cloud**: cloud security posture + workload threat protection (CNAPP); focused on protecting resources
- **Microsoft Sentinel**: cloud-native **SIEM/SOAR**; centralized security analytics, hunting, and incident response across the whole estate
- Defender for Cloud alerts can be **streamed into Sentinel** for correlation and response

## Pricing

- **Foundational CSPM is free** and on by default
- **Defender plans** (CWPP) and **Defender CSPM** are paid, enabled per resource type at the subscription level
- Billing is typically per resource (e.g. per server/hour, per database, per 10K transactions for storage)
- A free trial (typically 30 days) is available for Defender plans

