# Azure Blueprints

- Package of governance artifacts deployed together to create a repeatable, compliant environment
- Answers "how do I stand up a new subscription that is already governed"
- Free service, deployed at management group or subscription scope
- **Deprecated**: replaced by Template Specs + Deployment Stacks (see migration section)
- Still exam-relevant as the classic "environment in a box" / landing zone answer

## Blueprints vs ARM templates

- An ARM template deployment is a one-off, there is no link between the template and what it created
- A blueprint keeps an **active relationship** between the blueprint definition and the deployed resources
- That relationship enables compliance tracking, versioning and blueprint locks
- Blueprints orchestrate more than templates: they can also assign RBAC and Policy, and create resource groups

## Artifacts

| Artifact | Notes |
| --- | --- |
| Resource groups | Placeholders, name and location can be set as parameters at assignment |
| ARM templates | Deploy the actual resources (VNets, Log Analytics, storage, ...) |
| Policy assignments | Attach policies/initiatives to the new scope |
| Role assignments | RBAC for the new scope |

- Artifacts can be placed at subscription level or inside a blueprint resource group
- Artifacts have a defined **sequencing order** (`dependsOn`), so ordering is guaranteed
- Parameters can be **static** (fixed in the definition) or **dynamic** (supplied at assignment time)

## Lifecycle

1. **Create** the blueprint definition (saved at a management group or subscription)
2. **Publish** a version (versions are strings, for example `1.0`, `1.1`)
3. **Assign** a published version to a subscription (choose location, parameters, lock mode, managed identity)
4. **Update**: edit -> publish a new version -> re-assign the subscription to the new version
- The definition location (MG or subscription) determines which subscriptions can be assigned it
- Assignment needs `Owner` on the target subscription
- Uses a system-assigned or user-assigned managed identity to perform the deployment

## Blueprint locks

- Applied at assignment time, protect resources deployed by the blueprint
- Modes:
    - `Don't Lock` - no protection
    - `Read Only` - resources cannot be modified or deleted
    - `Do Not Delete` - resources can be modified but not deleted
- Stronger than normal resource locks: **even a subscription Owner cannot remove them**
- Only removable by updating/removing the blueprint assignment (the blueprint service owns the deny assignment)
- Implemented as a **deny assignment**, one of the few things that overrides RBAC allow

## Typical use cases

- New subscription onboarding / landing zone deployment
- Regulatory environments (built-in samples: ISO 27001, PCI-DSS, NIST SP 800-53, HIPAA/HITRUST, CIS, UK OFFICIAL)
- Enforcing a standard baseline: hub VNet peering, Log Analytics workspace, tags, allowed locations policy, RBAC groups

## Migration path (current guidance)

- Blueprints are deprecated, do not build new governance on them
- Replacements:
    - **Bicep / Terraform** - infrastructure as code for the resources
    - **Template Specs** - versioned, shareable templates stored as Azure resources
    - **Deployment Stacks** - keeps the lifecycle relationship and supports `deny settings` (the modern equivalent of blueprint locks)
    - **Azure Policy** - continues to handle the policy/compliance artifacts
    - **Azure Landing Zones (ALZ)** accelerators - Bicep/Terraform modules for the full CAF hierarchy

## Exam tips

- "Repeatable, governed environment for every new subscription" -> Blueprints (or ALZ + Deployment Stacks in newer wording)
- "Prevent even the subscription Owner from deleting deployed resources" -> Blueprint lock (deny assignment), not a resource lock
- "Track which subscriptions drifted from the standard" -> blueprint assignment compliance, not Policy alone
- Blueprints deploy a package, Policy continuously evaluates and enforces
