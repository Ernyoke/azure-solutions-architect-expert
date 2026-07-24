# Management Groups

- Containers for organizing multiple subscriptions
- Sit above subscriptions in the scope hierarchy
- Free of charge
- Used to apply governance (RBAC, Policy) to many subscriptions at once

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
- Deny always wins over allow in Policy, and RBAC is additive (deny assignments are the exception)

## Root management group access

- By default no user has access to the root management group
- A Global Administrator must "elevate access" to get `User Access Administrator` at root scope
- Assign only tenant-wide, non-negotiable controls at root (for example allowed locations, required tags)

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
