# Well-Architected Framework (WAF)

- Design principles and best practices for evaluating the quality of a **workload**
- Used to justify architecture trade-offs in design questions

## The 5 pillars

| Pillar | Focus | Typical Azure levers |
| --- | --- | --- |
| Reliability | Resiliency, availability, recovery | Availability zones, multi-region, ASR, Backup, health probes, retries |
| Security | Protect data and systems | Entra ID, RBAC, Key Vault, private endpoints, Defender for Cloud, encryption |
| Cost Optimization | Get the most value per spend | Right-sizing, reservations/savings plans, autoscale, tiering, spot VMs |
| Operational Excellence | DevOps and operations | IaC (Bicep/Terraform), CI/CD, Azure Monitor, alerts, runbooks |
| Performance Efficiency | Scale to meet demand | Autoscale, caching (Redis/CDN), partitioning, right SKU, async patterns |

## Trade-offs

- Pillars conflict: higher reliability (multi-region active-active) increases cost
- Every design decision should state which pillar it favors and what it costs
- Typical exam framing: "most cost-effective solution that meets an SLA" -> pick the cheapest option that still satisfies the reliability requirement

## Azure Advisor

- Free service that gives personalized recommendations based on resource telemetry
- Categories aligned to the WAF pillars:
    - **Cost** - idle/underutilized resources, reservation and savings plan purchase advice
    - **Security** - sourced from Defender for Cloud (secure score)
    - **Reliability** - availability improvements (zones, backup, replication)
    - **Operational Excellence** - subscription and service best practices
    - **Performance** - SKU and configuration improvements
- Scoped per subscription/resource group, results can be filtered and postponed/dismissed
- Advisor alerts can notify via action groups when new recommendations appear

## Related tools

- **Azure Advisor** - runtime recommendations for existing resources
- **WAF review (Assessment tool)** - questionnaire-based workload review
- **Defender for Cloud secure score** - security posture measurement
- **Cost Management + budgets** - cost pillar enforcement
