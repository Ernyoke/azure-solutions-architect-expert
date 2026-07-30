# Governance in Azure

- Governance = the set of rules and controls that keep resources compliant, secure and cost-effective
- Answers "what is allowed to be deployed, where, by whom and at what cost"
- Governance is applied top-down through the Azure scope hierarchy

## Scope hierarchy

- `Entra ID Tenant` -> `Management Groups` -> `Subscriptions` -> `Resource Groups` -> `Resources`
- Settings applied at a higher scope are inherited by all child scopes
- Inherited tools:
    - RBAC role assignments
    - Azure Policy assignments
    - Resource locks
    - Tags (only when enforced/inherited via policy, tags are NOT inherited by default)
- A tenant is the identity boundary, a subscription is the billing and quota boundary

## Governance building blocks

| Tool | Purpose |
| --- | --- |
| RBAC | Who can do what (actions on resources) |
| Azure Policy | What resources can look like (properties, locations, SKUs) |
| Resource locks | Prevent accidental delete/modify |
| Tags | Metadata for cost allocation and ownership |
| Cost Management + budgets | Spend visibility and alerting |
| [Blueprints](./azure-blueprints.md) (deprecated) | Package of artifacts for repeatable environments, replaced by Bicep/Terraform + Deployment Stacks + Template Specs |

- RBAC and Policy are complementary: RBAC allows an action, Policy can still deny the resulting resource

## Subscription design

- Subscriptions are the boundary for:
    - Billing and cost reporting
    - Quotas and service limits (scale limits per subscription per region)
    - Policy and RBAC segmentation
- Common reasons to split into multiple subscriptions:
    - Environment separation (prod / non-prod)
    - Business unit or cost center chargeback
    - Different compliance or sovereignty requirements
    - Hitting subscription-level quotas
- All subscriptions in a tenant trust the same Entra ID directory
- A subscription can be moved to another tenant or another management group

## Resource locks

- Two lock types:
    - `CanNotDelete` (Delete) - resource can be read and modified but not deleted
    - `ReadOnly` - resource can only be read, no modify and no delete
- Locks are inherited by child scopes, the most restrictive lock wins
- Locks apply to everyone, including subscription Owners (RBAC does not bypass them)
- Managing locks requires `Owner` or `User Access Administrator` (`Microsoft.Authorization/locks/*`)
- `ReadOnly` can break operations that use POST (for example listing storage account keys)
- Delete the lock before deleting the resource group or subscription content

## Moving resources

- Resources can move between resource groups, subscriptions and (some) tenants
- Not all resources support move (for example some networking and managed disk scenarios)
- Moving does NOT change the region, a region change requires redeploy + data migration
- Both source and target scope must be unlocked and the target must not violate policy

## Landing zones

- A landing zone is a pre-configured, governed environment where workloads are deployed
- Defined by the Cloud Adoption Framework (enterprise-scale architecture)
- Two categories:
    - **Platform landing zones** - shared services, usually separate subscriptions:
        - Identity (domain controllers, Entra Domain Services)
        - Management (Log Analytics, Automation, monitoring)
        - Connectivity (hub VNet, ExpressRoute/VPN gateways, Azure Firewall, DNS)
    - **Application landing zones** - subscriptions for individual workloads
- Landing zones ship with a management group hierarchy, policy assignments, RBAC and networking baked in

## Azure Lighthouse

- Cross-tenant management (Azure delegated resource management)
- A service provider manages a customer tenant without creating accounts in it
- Customer delegates a scope (subscription or resource group) with specific RBAC roles
- Onboarded via ARM template or a Managed Services offer in Azure Marketplace
- Customer keeps full control and can remove the delegation at any time

## Governance best practices

- Define a naming convention and enforce it with policy (deny or modify)
- Require tags (`Environment`, `Owner`, `CostCenter`) via `Modify` + `Deny` policies
- Assign policy at management group level, exclude exceptions instead of assigning per subscription
- Use PIM for privileged roles instead of standing access
- Use locks on shared/platform resources (hub VNet, Key Vault, storage with backups)
- Review compliance and secure score regularly (Policy compliance, Defender for Cloud, Advisor)
