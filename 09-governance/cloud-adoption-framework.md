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

## Tools per methodology

| Phase | Typical tooling / deliverable |
| --- | --- |
| Strategy | Business case, TCO calculator, Pricing calculator |
| Plan | Azure Migrate (discovery, dependency mapping, assessment), digital estate inventory, skilling plan |
| Ready | Landing zone (ALZ accelerator, Bicep/Terraform), management group hierarchy, naming and tagging standard |
| Adopt | Azure Migrate: Server/Database/Web app migration, Data Box, Site Recovery, App Service Migration Assistant |
| Govern | [Azure Policy](./azure-policy.md), [locks](./governance.md), Cost Management + budgets, PIM |
| Manage | Azure Monitor, Log Analytics, Update Manager, Backup, Service Health |
| Secure | Defender for Cloud, secure score, Sentinel, Key Vault |

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

## Govern - the governance MVP

- Start with a **minimum viable product** of guardrails, then expand as risk grows
- Iterative loop: define corporate policy -> implement with Azure tooling -> monitor compliance -> adjust
- Corporate policy = business risk + policy statements + design guidance
- Enforced mainly through management groups, Azure Policy, RBAC and budgets

## Landing zone design areas

- Azure billing and Entra ID tenant
- Identity and access management
- Resource organization (management groups, subscriptions, naming, tagging)
- Network topology and connectivity (hub-spoke or Virtual WAN)
- Security
- Management and monitoring
- Governance (Azure Policy)
- Platform automation and DevOps

## Landing zone implementation options

- **Start small and expand** - a few subscriptions and basic policies, grow governance over time
- **Enterprise-scale / Azure Landing Zone (ALZ) accelerator** - full conceptual architecture deployed up front
- ALZ default management group hierarchy under the tenant root:
    - `Intermediate root` (e.g. `Contoso`)
        - `Platform` -> `Identity`, `Management`, `Connectivity`
        - `Landing zones` -> `Corp` (private, connected to hub), `Online` (internet-facing)
        - `Sandbox` - loose policy for experimentation, no connectivity to prod
        - `Decommissioned` - workloads being retired
- Deployed with Bicep/Terraform (ALZ modules) or the Azure portal accelerator
- See [Governance in Azure](./governance.md#landing-zones) for platform vs application landing zones

## Organizational alignment

- **Cloud Strategy Team** - business stakeholders, owns motivations and outcomes
- **Cloud Adoption Team** - migrates and builds workloads
- **Cloud Platform Team** - builds and runs the landing zones and shared services
- **Cloud Governance Team** - policies, compliance, cost guardrails
- **Cloud Center of Excellence (CCoE)** - platform + governance + automation working as an enabling team
- RACI style split: central IT owns the platform, workload teams own their application landing zone

## Exam tips

- CAF = adoption and governance of the **estate**, [WAF](./well-architected-framework.md) = quality of a **workload**
- "Ready" phase deliverable = landing zone
- Rationalization decisions (rehost vs refactor) usually come down to time, cost and app changes allowed
- Discovery/assessment question -> **Azure Migrate**; cost justification question -> **TCO calculator**
- "Consistent, compliant environment for new workloads" -> landing zone, not per-resource policy
- Sandbox management group = experimentation with relaxed policy, isolated from production
