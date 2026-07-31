# Azure Site Recovery (ASR)

- Azure Site Recovery is a disaster recovery service that replicates machines and orchestrates failover and failback
- It is **not a backup service**: replication provides recent recovery copies, while Azure Backup provides historical recovery points
- Supported scenarios include:
    - Azure VMs between Azure regions
    - Supported VMware VMs, Hyper-V VMs and physical servers to Azure
    - Supported Windows and Linux operating systems
- Replication health and recovery points are managed through a Recovery Services vault

## Replication

- A replication policy defines settings such as recovery-point retention and application-consistent snapshot frequency
- **Crash-consistent recovery points** capture disk state without coordinating applications
- **Application-consistent recovery points** use application-aware mechanisms where supported and are preferable for transactional workloads
- Replication is asynchronous, so the latest recoverable point can be behind the source workload
- Target settings include region, resource group, VNet, subnet, availability options, VM size and managed disks

## Failover Types

| Operation | Source state | Data loss expectation | Purpose |
| --- | --- | --- | --- |
| Test failover | Remains online | None to production | Validate recovery in an isolated network |
| Planned failover | Available and synchronized | Typically no data loss | Controlled migration or maintenance |
| Unplanned failover | Unavailable or not synchronized | Possible | Respond to an outage |

- After an unplanned failover, choose the most appropriate recovery point:
    - Latest processed: lowest RPO
    - Latest application-consistent: application-aware state
    - Custom: a selected recovery point
- **Commit** completes the failover and removes other available recovery points
- **Re-protect** reverses replication direction in preparation for failback

## Recovery Plans

- A recovery plan groups protected machines and defines startup order
- Machines in the same group start in parallel; groups run sequentially
- Add Azure Automation runbooks and manual actions for tasks such as DNS changes, database checks and validation
- Recovery plans should reflect application dependencies, not only server names
- Use separate plans when workloads have different owners, RTOs or recovery procedures

## Testing and Failback

- Run test failovers regularly in an isolated VNet to avoid duplicate IP addresses or production traffic
- Validate authentication, DNS, network security, database consistency, application transactions, monitoring and performance
- Clean up the test failover after validation so test resources do not continue incurring cost
- After the primary location is healthy, re-protect the workload, synchronize changes, perform a planned failback and validate again

## Design Considerations

- ASR protects supported machine workloads; use application-native replication for services such as Azure SQL or Cosmos DB
- Confirm support for operating systems, disk types, encryption, network appliances and application consistency
- Reserve target-region quotas and capacity before a disaster
- Pre-create or automate target networking, security, load balancing, DNS and private endpoints
- Use Azure Backup as well as ASR when historical restore points or long-term retention are required
- Monitor replication health and test whether the design meets business RTO, RPO and RLO

## Exam Design Decisions

- Non-disruptive DR validation -> **test failover**
- Controlled move with source available -> **planned failover**
- Source region unavailable -> **unplanned failover**
- Multi-tier startup order and automation -> **recovery plan**
- Reverse replication before returning -> **re-protect**, then planned failback
- Historical recovery after corruption -> **Azure Backup**, not ASR alone
