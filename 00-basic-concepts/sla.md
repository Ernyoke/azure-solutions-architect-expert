# SLA - Service Level Agreement

- It is the uptime percentage of a cloud service
- SLA examples:
    - 95% - downtime >18 days over a year
    - 99% - downtime >3d days over a year
    - 99.9% - downtime ~9h
    - 99.99% - downtime ~52min
- Example for SLAs:
    - App Service has a 99.95% SLA. No SLA for Apps under Free or Shared Tiers
    - Azure SQL: Premium Tier: 99.995% SLA

## SLA Calculation

- Example:
    - App Service SLA: 99.95%
    - Azure SQL SLA: 99.99%
    - Actual SLA: 99.95 * 99.99 = 99.94% ~ 5h annual downtime