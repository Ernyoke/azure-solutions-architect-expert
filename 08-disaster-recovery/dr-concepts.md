# Disaster Recovery Concepts

## Hot, Warm and Cold Recovery

- **Hot site**:
    - Runs a fully provisioned environment with continuously or frequently replicated data
    - Supports rapid, often automated failover with the lowest RTO and RPO
    - Does not guarantee zero downtime or zero data loss unless the architecture explicitly provides it
    - Has the highest cost because duplicate infrastructure remains operational
- **Warm site**:
    - Maintains a partially provisioned environment that must be scaled or configured during recovery
    - Provides moderate RTO, RPO and cost
    - Commonly uses asynchronous replication and automation to complete deployment
- **Cold site**:
    - Provides little or no pre-provisioned compute infrastructure
    - Requires resources, applications and data to be restored before service resumes
    - Has the highest RTO and typically a higher RPO, but the lowest ongoing cost

## RPO, RTO and RLO

- **Recovery Point Objective (RPO)**:
    - Maximum acceptable amount of data loss, measured as time before the outage
    - Determines the required backup or replication frequency
    - Example: an RPO of 15 minutes means up to 15 minutes of data can be lost
- **Recovery Time Objective (RTO)**:
    - Maximum acceptable time to restore a workload after an outage
    - Includes detection, failover or restore, validation and service resumption
    - Example: an RTO of 2 hours means the workload must be operational within 2 hours
- **Recovery Level Objective (RLO)**:
    - Required scope and granularity of recovery
    - Defines whether recovery must restore an entire environment, an application, a VM, a database, a file or a point in time
    - The selected recovery solution must support the required recovery level

## Backup and Disaster Recovery

- Backup creates retained recovery points for restoring deleted, corrupted or historical data
- Replication maintains a synchronized copy of a workload for failover and usually provides lower RPO and RTO
- Azure Backup is primarily a backup-and-restore service
- Azure Site Recovery provides workload replication and orchestrated failover and failback
- A complete business continuity and disaster recovery strategy can use both services
