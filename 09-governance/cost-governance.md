# Cost Governance

- Governance of spend: visibility, allocation, control and optimization
- Basic pricing models are covered in [Cost Management](../00-basic-concepts/cost.md)

## Billing hierarchy

- **EA**: Billing account -> Department -> Enrollment account -> Subscription
- **MCA**: Billing account -> Billing profile (invoice) -> Invoice section -> Subscription
- The subscription is always the cost boundary, cost is reported per subscription
- Management groups can be used as a scope for aggregated cost analysis

## Cost Management

- **Cost analysis** - interactive spend breakdown by scope, resource, resource group, tag, service, location
- **Amortized vs actual cost** - amortized spreads reservation purchases across the term, actual shows the upfront charge
- **Exports** - scheduled push of cost data to a storage account (daily/weekly/monthly) for BI/chargeback
- **Cost allocation rules** - redistribute shared costs (for example a shared AKS cluster) to consuming teams
- **Showback vs chargeback** - report cost to teams vs actually bill them, both depend on tagging

## Budgets

- Set at management group, subscription or resource group scope
- Can be based on **cost** or **usage**
- Reset periods: monthly, quarterly, annually
- Alert thresholds defined as a % of the budget (actual spend or forecasted spend)
- Actions on threshold:
    - Email notification / action group
    - Trigger an Automation runbook or Logic App (for example shut down dev VMs)
- Budgets do **not** stop spending, they only alert and trigger automation

## Optimization levers

| Lever | Saving |
| --- | --- |
| Reserved Instances (1 or 3 years) | Up to ~72% vs pay-as-you-go |
| Savings Plans for compute (1 or 3 years) | Flexible across VM series/regions, slightly lower discount |
| Azure Hybrid Benefit | Reuse on-prem Windows Server / SQL Server licenses |
| Spot VMs | Up to ~90% for interruptible workloads |
| Autoscale / auto-shutdown | Pay only for what is running |
| Storage access tiers + lifecycle rules | Move cold data to Cool/Cold/Archive |
| Right-sizing (Advisor) | Remove idle and oversized resources |
| Dev/Test subscription pricing | Discounted rates for non-production |

## Tagging for cost

- Tags are the main mechanism for cost allocation (`CostCenter`, `Owner`, `Environment`, `Project`)
- Tags are **not** inherited by default - enforce with Azure Policy:
    - `Modify` effect to inherit a tag from the resource group or subscription
    - `Deny` effect to block resources created without required tags
- Not every resource type supports tags, and not all costs are tag-attributable

## Guardrails

- Assign Policy to deny expensive SKUs and non-approved regions
- Use quotas/limits per subscription to cap blast radius
- Separate subscriptions per environment so prod and dev spend are cleanly split
- Review Advisor cost recommendations and reservation utilization regularly
