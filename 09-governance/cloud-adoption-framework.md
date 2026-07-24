# Cloud Adoption Framework (CAF)

- Microsoft's guidance for the **whole cloud journey**: business, people, process and technology
- Answers "how do we get to the cloud and run it" (WAF answers "how do we build a good workload")

## Methodologies

| Phase | Purpose |
| --- | --- |
| Strategy | Define motivations, business outcomes and justification |
| Plan | Digital estate inventory, rationalization, skills and adoption plan |
| Ready | Prepare the environment: landing zones, naming, tagging, subscriptions |
| Adopt (Migrate / Innovate) | Move existing workloads or build new cloud-native ones |
| Govern | Ongoing guardrails and policy enforcement |
| Manage | Operations, monitoring, support and business commitments |
| Secure | Security posture, controls and incident response |

## The 5 Rs of rationalization (Plan phase)

- **Rehost** - lift and shift to IaaS (fastest, Azure Migrate / Site Recovery)
- **Refactor** - minimal changes to move to PaaS (App Service, Azure SQL)
- **Rearchitect** - redesign for cloud-native (microservices, containers)
- **Rebuild** - rewrite from scratch
- **Replace** - swap for SaaS
- Additional options often listed: **Retain** (keep on-prem) and **Retire** (decommission)

## Govern methodology - 5 disciplines

- **Cost Management** - budgets, cost allocation, optimization
- **Security Baseline** - encryption, network controls, compliance requirements
- **Resource Consistency** - naming, tagging, resource organization, monitoring onboarding
- **Identity Baseline** - RBAC, least privilege, hybrid identity
- **Deployment Acceleration** - IaC templates, repeatable deployments, DevOps

## Landing zone design areas

- Azure billing and Entra ID tenant
- Identity and access management
- Resource organization (management groups, subscriptions, naming, tagging)
- Network topology and connectivity (hub-spoke or Virtual WAN)
- Security
- Management and monitoring
- Governance (Azure Policy)
- Platform automation and DevOps

## Exam tips

- CAF = adoption and governance of the **estate**, WAF = quality of a **workload**
- "Ready" phase deliverable = landing zone
- Rationalization decisions (rehost vs refactor) usually come down to time, cost and app changes allowed
