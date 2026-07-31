# Management Groups

- Containers for organizing multiple subscriptions
- Sit above subscriptions in the scope hierarchy
- Free of charge
- Used to apply governance (RBAC, Policy) to many subscriptions at once
- They are a governance scope, not a deployment, billing or quota boundary
- Resources and resource groups cannot be placed directly in a management group; they remain inside subscriptions

## Scope hierarchy

- `Tenant Root Group` -> `Management Group` -> `Subscription` -> `Resource Group` -> `Resource`
- A management group can contain subscriptions and child management groups
- A subscription can belong to only one management group at a time
- New subscriptions are placed under the tenant root group by default unless a default management group is configured
- A management group has an immutable ID and a changeable display name

## Key characteristics

- A `Tenant Root Group` is created automatically and cannot be moved or deleted
- Every management group and subscription has exactly one parent
- Up to 10,000 management groups per directory
- Up to 6 levels of depth (not counting the root level and the subscription level)
- A management group can contain other management groups and/or subscriptions
- All subscriptions in a management group must trust the same Entra ID tenant

## Inheritance

- RBAC assignments made at management group level flow down to all child subscriptions
- Policy assignments made at management group level are evaluated in all child subscriptions
- RBAC role assignments are additive across scopes; Azure deny assignments take precedence over role assignments
- A Policy `Deny` effect can block a deployment even when RBAC permits the user to perform the action
- Child scopes can receive additional RBAC and Policy assignments, but inherited assignments remain effective

## Access and authorization

- Management group operations use Azure RBAC, not Microsoft Entra directory roles
- Common roles at management group scope:
	- `Management Group Contributor` - create, rename, delete and reorganize management groups, but cannot manage access
	- `Resource Policy Contributor` - create and assign policies
	- `User Access Administrator` - manage role assignments
	- `Owner` - manage the scope and its access
- Creating a child management group requires write permission on the parent
- Moving a management group requires write permission on the item being moved and both the source and destination parents
- Moving a subscription requires permission to remove it from the current parent, add it to the destination and update the subscription management-group association

## Root management group access

- By default no user has access to the root management group
- A Global Administrator must "elevate access" to get `User Access Administrator` at root scope
- Elevating access grants authorization at `/`, allowing access management across subscriptions and management groups in the tenant; remove it after use
- Assign only tenant-wide, non-negotiable controls at root (for example allowed locations or mandatory security controls)

## Moving and deleting

- Moving a subscription changes the policies and RBAC assignments it inherits from management groups
- Validate inherited policy before a move; workloads may become non-compliant or future deployments may be denied
- A management group must be empty before it can be deleted
- The tenant root group cannot be moved or deleted
- Deleting a management group does not delete its subscriptions; move the subscriptions to another parent first
- A subscription can be moved between management groups in the same tenant without moving its resources

## Typical hierarchy

```
Tenant Root Group
├── Platform
│   ├── Identity
│   ├── Management
│   └── Connectivity
├── Landing Zones
│   ├── Corp (internal, no internet exposure)
│   └── Online (internet-facing)
├── Sandbox
└── Decommissioned
```

- `Sandbox` = relaxed policies for experimentation, isolated from production networks
- `Decommissioned` = subscriptions being retired, heavily restricted

## Design guidance

- Design the hierarchy around **policy and access needs**, not around the org chart
- Keep the hierarchy flat, deep trees make troubleshooting inheritance hard
- Do not mix prod and non-prod under the same management group if policies differ
- Move subscriptions instead of re-assigning policies one by one
- Put organization-wide security controls at higher scopes and workload-specific controls closer to application subscriptions
- Use management groups for stable governance archetypes such as platform, corporate, online and sandbox environments
- Avoid granting broad RBAC roles at the tenant root because every child scope inherits them

## Exam decision points

- Need one Policy or RBAC assignment across many subscriptions -> assign it to their common management group
- Need separate billing, quota or isolation boundaries -> use separate subscriptions, then organize them with management groups
- Need different guardrails for internet-facing and internal workloads -> place their subscriptions under different management groups
- Need to deploy resources -> target a subscription or resource group, not a management group
- Need cross-tenant delegated administration -> use Azure Lighthouse; a management group hierarchy does not cross Entra tenants
