# Business Continuity Design

- Business continuity combines **high availability**, **backup and restore**, and **disaster recovery**
- These controls solve different failures and are often used together:
    - High availability keeps a service running through local component or zone failures
    - Backup restores an earlier copy after deletion, corruption or ransomware
    - Disaster recovery restores service in another location after a major outage
- Define recovery objectives per workload before selecting a service:
    - **RTO**: maximum acceptable time to restore service
    - **RPO**: maximum acceptable data loss measured in time
    - **RLO**: minimum functionality or capacity required after recovery

## Design Process

1. Inventory components, dependencies and data flows
2. Classify workload criticality and failure scenarios
3. Define RTO, RPO, RLO, retention and compliance requirements
4. Remove single points of failure within the primary region
5. Select backup and cross-region recovery mechanisms
6. Define dependency order, DNS/routing changes and operating procedures
7. Test recovery regularly and measure achieved RTO/RPO
8. Update the design when dependencies or business requirements change

## Scope of Failure

| Failure scope | Typical protection |
| --- | --- |
| Process or instance | Health probes, retries, multiple instances |
| Rack/datacenter | Availability zones or zone-redundant service |
| Region | Multi-region deployment and replicated data |
| Accidental deletion/corruption | Soft delete, versioning, backup and point-in-time restore |
| Malicious deletion/ransomware | Isolated backup, immutability, soft delete and authorization controls |

## Compute Availability and Recovery

| Workload | High availability | Regional disaster recovery |
| --- | --- | --- |
| Azure VMs | Availability zones or availability sets; VMSS for scale | Azure Site Recovery to another region; Azure Backup for restore |
| App Service | Multiple instances, zone redundancy on supported plans/regions | Deploy to a second region and route with Front Door or Traffic Manager |
| AKS | Multiple nodes and zones; multiple replicas and pod disruption budgets | Separate cluster in another region, replicated registry/data and global routing |
| Container Apps | Multiple replicas and zone redundancy where supported | Separate environment in another region and global routing |
| Functions | Platform scaling and zone redundancy on supported plans | Deploy the function and dependencies to another region |

- Availability sets protect VMs from host and maintenance-domain failures but not an availability-zone failure
- Availability zones provide datacenter-level isolation within one region
- Stateless compute is easier to recreate in another region through infrastructure as code
- Do not assume a regional compute deployment protects its database, secrets, registry, DNS or external dependencies

## Relational Data

### Azure SQL Database

- Built-in local replicas provide service availability; enable **zone redundancy** on supported tiers for zone failure protection
- **Active geo-replication** creates up to four readable secondary databases and supports application-controlled failover per database
- **Failover groups** coordinate failover for one or more databases and provide stable read-write and read-only listener endpoints
- Failover groups support customer-managed failover and policy-based automatic failover after a grace period
- Geo-replication is asynchronous, so forced failover can lose recent transactions
- Automated backups provide PITR; geo-redundant backup storage enables geo-restore when no live secondary exists
- Active geo-replication/failover groups provide lower RTO/RPO at higher cost; geo-restore is cheaper but slower

### Azure SQL Managed Instance

- Use zone redundancy where supported for in-region availability
- Use an instance failover group for replication and failover to a managed instance in another region
- Automated PITR/LTR backups protect against logical corruption and deletion

### SQL Server on Azure VMs

- Use Always On availability groups for database-level HA or failover cluster instances for instance-level HA
- Place replicas across zones or fault domains and use an Azure Load Balancer where the selected topology requires it
- Use Azure Site Recovery for VM-level regional recovery when application-level SQL replication is not appropriate
- Use Azure Backup for SQL Server in Azure VMs or native backup to URL for point-in-time recovery

### Azure Database for PostgreSQL/MySQL

- Use zone-redundant high availability where supported for automatic in-region standby failover
- Read replicas improve read scale and can support regional recovery, but promotion and endpoint changes must be planned
- Automated backups and geo-redundant backup options provide point-in-time or regional restore according to service capabilities
- A read replica is not a replacement for backup because logical corruption can replicate

## Semi-Structured Data

### Azure Cosmos DB

- Distribute an account to multiple regions for regional availability and lower user latency
- Single-write accounts use service-managed failover priorities; automatic failover can promote the next region
- Multi-region writes allow writes in every configured region and avoid a single write-region dependency
- Consistency level affects availability, latency and data guarantees during failures
- Availability-zone support protects replicas from zone failure within supported regions
- Continuous backup provides point-in-time restore; periodic backup provides retention-based recovery points
- Multi-region replication does not replace backup because accidental writes and deletes replicate

## Unstructured Data

### Azure Storage Redundancy

| Option | Copies | Protects against | Read secondary |
| --- | --- | --- | --- |
| LRS | Three in one datacenter | Local hardware failure | No |
| ZRS | Three across zones in one region | Zone failure | No |
| GRS | LRS primary + asynchronous LRS secondary | Regional failure | No until failover |
| RA-GRS | GRS | Regional failure | Yes |
| GZRS | ZRS primary + asynchronous LRS secondary | Zone and regional failure | No until failover |
| RA-GZRS | GZRS | Zone and regional failure | Yes |

- Geo-replication is asynchronous; the **last synchronization time** bounds potential data loss during customer-managed failover
- Customer-managed account failover changes the secondary into the primary and can cause data loss
- Microsoft-managed failover is reserved for severe regional events and has no guaranteed initiation time
- Use ZRS/GZRS for workloads that need zone continuity; LRS/GRS primary storage can be unavailable during a datacenter or zone outage

### Blob and File Protection

- Blob soft delete recovers deleted or overwritten blobs for a retention period
- Blob versioning preserves previous versions; lifecycle rules can control their cost
- Change feed records blob changes and supports audit or custom recovery workflows
- Immutable storage with time-based retention or legal hold protects write-once-read-many data
- Operational backup for blobs provides policy-based continuous protection and point-in-time recovery within supported limits
- Azure Files supports share snapshots, soft delete and Azure Backup
- Azure File Sync can cache Azure file shares on Windows Servers but is not itself a backup solution

## Backup Architecture

| Workload | Recommended protection |
| --- | --- |
| Azure VM | Azure VM Backup in a Recovery Services vault |
| SQL/SAP HANA in Azure VM | Workload-aware Azure Backup |
| Azure managed disks | Azure Disk Backup in a Backup vault |
| Azure Blobs | Operational/vaulted backup in a Backup vault as supported |
| Azure Files | Azure Backup plus snapshots/soft delete |
| On-premises files/folders | MARS agent |
| On-premises application-aware workloads | MABS or DPM |
| Azure SQL Database/Managed Instance | Built-in automated PITR and LTR backups |

- Choose vault LRS for lower cost when regional loss is addressed elsewhere; choose GRS and enable Cross Region Restore when recovery in the paired region is required
- Use soft delete, immutability, multifactor authorization and Resource Guard to protect backup operations from malicious deletion
- Backup retention must meet legal and business requirements without retaining sensitive data longer than permitted

## Recovery Orchestration

- Define a recovery plan that orders identity, DNS, network, data, middleware and front-end components
- Automate repeatable infrastructure and configuration deployment
- Use health probes and global routing to direct traffic only after the recovery environment is ready
- Document planned failover, unplanned failover, failback and data reconciliation
- Run non-disruptive tests and verify application behavior, not merely that resources start
- Measure actual RTO/RPO and retain evidence for audit and improvement

## Exam Design Decisions

- Host/datacenter protection for VMs -> **availability set**
- Datacenter-level fault isolation -> **availability zones**
- VM regional replication and orchestrated failover -> **Azure Site Recovery**
- Recover an earlier copy after corruption -> **backup/PITR**, not replication alone
- Azure SQL low RTO cross-region failover with stable endpoints -> **failover group**
- Lowest-cost Azure SQL regional recovery -> **geo-restore from geo-redundant backup**
- Cosmos DB active-active writes -> **multi-region writes**
- Storage zone + region protection -> **GZRS/RA-GZRS**
- Read from a storage secondary before failover -> **RA-GRS/RA-GZRS**
- Protect blobs from deletion/corruption -> **soft delete + versioning/backup**