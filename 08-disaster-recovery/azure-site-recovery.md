# Azure Site Recovery (ASR)

- It is a hybrid (on-premises to cloud) backup solution for site-to-site recovery
- ASR is a critical component for business continuity and disaster recovery strategy
- ASR replicates workloads from a primary site to a secondary site
- In case primary site suffers a failure, Site Recovery will fail-over to the secondary site to ensyre continuity of services
- Azure Site Recovery can replicate:
    - Azure VMs between regions (cross-region replication)
    - Windows, Any OS and Linuc
    - On-premises to Azure
    - Between other cloud service providers (e.g. AWS to Azure)
    - VMWare, Hyper-V or Physical Machines

## Recovery Solution

- A recovery solution for Azure, hybrid and on-premises workloads should bne desinged to meet specific objectives, including RTO, RPO and RLO
- When recommending a recovery solution for Azure, hybrid and on-premises, the following factors should be considered:
    - Business requirements: the solution should align with the business requirements, including RTO, RLO and RPO objectives
    - Workload types: the solution should be able to accomodate the requirements of each workload type for RTO/RPO/RLO
    - Hybrid or on-premises: the solution should be able to handle hybrid or on-premises workloads depending on the specific requirements of the organization
    - Data protection: the solution should provide data protection, including backups and replication
    - Testing: the solution should be tested regularly
