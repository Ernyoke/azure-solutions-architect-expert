# Azure Policy

- Enforces organizational standards and evaluates compliance of resources
- Controls **what a resource can look like** (RBAC controls **who can do what**)
- The service itself is free (Policy Guest Configuration for in-VM auditing is paid)
- Works at management group, subscription and resource group scope, and is inherited

## Components

- **Policy definition** - a single rule written in JSON (`if` condition + `then` effect)
- **Initiative (policy set)** - a group of policy definitions assigned together
- **Assignment** - applying a definition/initiative to a scope
- **Parameters** - make definitions reusable (for example list of allowed locations/SKUs)
- **Exclusion** - a child scope removed from the assignment (not evaluated at all)
- **Exemption** - a resource is evaluated but marked as compliant, can be time-bound with an expiry date

## Effects

| Effect | Behavior |
| --- | --- |
| `Deny` | Blocks the create/update request |
| `Audit` | Allows the request, flags it as non-compliant |
| `Append` | Adds fields to the resource during creation |
| `Modify` | Adds/updates/removes properties or tags (also works on existing resources via remediation) |
| `AuditIfNotExists` | Audits when a related resource is missing (for example no diagnostic settings) |
| `DeployIfNotExists` (DINE) | Deploys the missing related resource automatically |
| `DenyAction` | Blocks a specific action, mainly used to block delete |
| `Manual` | Attestation-based compliance for non-technical controls |
| `Disabled` | Turns off the rule without deleting the assignment |

- `Deny` is evaluated before the request reaches the resource provider
- `DeployIfNotExists` and `Modify` require a **managed identity** on the assignment with enough RBAC rights
- Remediation tasks apply DINE/Modify effects to **already existing** resources

## Evaluation

- Triggered on resource create/update (immediate)
- On new or changed assignment: takes around 30 minutes before evaluation starts
- Standard compliance scan runs every 24 hours
- Can be triggered on demand (`Start-AzPolicyComplianceScan` / REST)
- Compliance state is visible in the Policy blade, per assignment and per resource

## Common use cases

- Allowed locations (data residency)
- Allowed VM SKUs / resource types
- Require tags and inherit tags from the resource group (`Modify`)
- Require HTTPS-only on storage accounts and App Service
- Deny public IP or public blob access
- Auto-deploy diagnostic settings to a Log Analytics workspace (`DeployIfNotExists`)
- Enforce private endpoints and deny public network access
- Enforce backup on VMs

## Built-in initiatives

- Microsoft Cloud Security Benchmark (MCSB) - assigned by default with Defender for Cloud
- Regulatory compliance packs: ISO 27001, PCI DSS, NIST SP 800-53, HIPAA, CIS
- Compliance results surface in the Defender for Cloud regulatory compliance dashboard

## Limits worth remembering

- 200 policy or initiative assignments per scope
- 500 policy definitions per management group, 500 per subscription
- 100 parameters per policy definition

## Design guidance

- Assign at the highest scope that makes sense (management group), use exclusions for exceptions
- Start with `Audit`, review the compliance report, then switch to `Deny`
- Group related definitions into an initiative so a single assignment covers a control set
- Use `DeployIfNotExists` for guardrails that should self-heal (monitoring, backup, encryption)
- Policy does not remove existing non-compliant resources, it only flags them until remediation runs

## Azure Policy vs RBAC vs Locks

- **RBAC** - identity-based, controls the allowed actions
- **Policy** - resource-based, controls the allowed resource properties
- **Locks** - scope-based, blocks delete/modify regardless of RBAC
