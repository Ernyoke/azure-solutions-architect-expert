# Migration Design

- A migration design starts with business outcomes, workload dependencies, downtime tolerance, compliance, cost and required application changes
- Use the Cloud Adoption Framework (CAF) to organize strategy, planning, landing-zone readiness, migration and ongoing governance
- Do not move production workloads until identity, networking, governance, security and monitoring are ready in an Azure landing zone

## Migration Lifecycle

1. **Discover** the digital estate and workload dependencies
2. **Assess** Azure readiness, sizing, cost, compatibility and risks
3. **Rationalize** each workload by selecting a migration strategy
4. **Prepare** the landing zone and remediate blockers
5. **Replicate or transfer** the workload and its data
6. **Test** in an isolated network without affecting production
7. **Cut over** during an approved window and control final data synchronization
8. **Validate** functionality, performance, security, monitoring and recovery
9. **Decommission** the source only after the rollback period expires
10. **Optimize** cost, reliability, operations and architecture after migration

## Migration Strategies

| Strategy | Meaning | Typical target |
| --- | --- | --- |
| Rehost | Move with minimal changes | Azure VMs |
| Refactor | Make limited changes to use managed services | App Service or Azure SQL |
| Rearchitect | Redesign for cloud-native scale and resilience | AKS, Container Apps, Functions, Cosmos DB |
| Rebuild | Rewrite the application | New cloud-native application |
| Replace | Adopt a SaaS product | Microsoft 365 or Dynamics 365 |
| Retain | Keep the workload in its current location | On-premises or another cloud |
| Retire | Decommission the workload | No target |

- Rehost is usually fastest but preserves operational burden and technical debt
- Refactor or rearchitect requires more effort but can reduce management and improve scalability
- Rationalize applications by business value and migration difficulty, not only by server count

## Azure Migrate

- Azure Migrate is the central hub for discovery, assessment and migration tooling
- A project stores discovery metadata and assessment results; it does not store migrated production data
- The **Azure Migrate appliance** discovers servers and sends metadata to the project
- Agentless discovery can collect:
    - Hardware inventory and utilization
    - Installed applications and roles
    - Dependency data
    - SQL Server instances and databases
    - ASP.NET and Java web applications where supported
- Assessments provide:
    - Azure readiness and remediation guidance
    - Right-sized or as-on-premises sizing
    - Estimated compute and storage cost
    - Confidence rating based on available performance data
    - Suitability for targets such as Azure VMs, Azure VMware Solution, Azure SQL and App Service
- Use dependency analysis to define migration groups and avoid moving an application without required upstream or downstream systems

## Server Migration

- Azure Migrate: Server Migration supports VMware, Hyper-V, physical servers and servers in other clouds
- Replication continuously copies changed data to Azure before cutover
- A **test migration** creates an Azure VM in an isolated test VNet while source replication continues
- Validate boot, connectivity, application function, performance and security during testing
- At cutover:
    1. Notify users and stop or quiesce writes when required
    2. Perform final synchronization
    3. Create the Azure VM and validate it
    4. Update DNS, routes or load-balancer targets
    5. Keep the source available for rollback until acceptance criteria are met
- Use Azure Site Recovery primarily for ongoing disaster recovery; use Azure Migrate for a planned migration with discovery and assessment

## Web Application Migration

- Assess application runtime, framework version, operating-system dependencies, authentication, certificates, local files, sessions and network dependencies
- App Service is a good target for supported HTTP applications that do not require operating-system control
- App Service Migration Assistant can assess and migrate supported .NET web applications to App Service
- Containerize applications with incompatible runtimes or portable dependencies and target Container Apps or AKS
- Keep applications on VMs when they require unsupported software, kernel access, custom agents or full machine control
- Replace local session and file state with external services such as Redis and Storage before scaling across instances

## Database Migration

- Use Azure Migrate assessments to identify compatibility issues, target recommendations and sizing
- Choose the target before choosing the migration tool:
    - Azure SQL Database for modern applications needing database-level PaaS
    - Azure SQL Managed Instance for high SQL Server instance compatibility
    - SQL Server on Azure VMs when operating-system or full SQL Server control is required
    - Azure Database for PostgreSQL/MySQL for supported open-source database workloads
- Azure Database Migration Service (DMS) orchestrates migrations to supported Azure database targets
- **Offline migration** stops source writes while data is moved; it is simpler but has longer downtime
- **Online migration** performs continuous synchronization and a short final cutover; it reduces downtime but adds complexity and support constraints
- Before cutover, test schema, logins, jobs, extensions, collation, connection strings, performance and application compatibility
- After cutover, validate data consistency and establish Azure-native backup, monitoring, security and high availability

## Unstructured Data Migration

| Requirement | Recommended tool |
| --- | --- |
| Scripted online copy to or from Azure Storage | AzCopy |
| Large recurring or managed file migrations | Azure Storage Mover |
| ETL, transformation or orchestration across data stores | Azure Data Factory |
| Network transfer of NFS data into Blob Storage | Azure Data Box Gateway |
| Limited bandwidth or very large offline transfer | Azure Data Box family |
| Synchronize an on-premises Windows file server with Azure Files | Azure File Sync |

- Estimate data volume, available bandwidth, change rate and transfer window before selecting online or offline transfer
- For an online seed, perform an initial bulk copy followed by incremental synchronization before cutover
- Validate file counts, checksums, metadata, ACLs and application behavior
- Account for transfer egress charges, API transaction costs and the performance tier of the destination

## Migration Waves

- Start with low-risk pilot workloads to validate the landing zone, process and operational model
- Group tightly coupled components in the same migration wave
- Sequence shared services such as identity, DNS and connectivity before dependent applications
- Define entry criteria, exit criteria, owners, communication, rollback triggers and a maximum rollback window for each wave
- Avoid decommissioning source systems until backup, monitoring, DR and business acceptance are confirmed

## Exam Design Decisions

- Inventory and dependency mapping -> **Azure Migrate discovery**
- Readiness, sizing and cost estimate -> **Azure Migrate assessment**
- Planned lift-and-shift server move -> **Azure Migrate: Server Migration**
- Ongoing cross-region VM recovery -> **Azure Site Recovery**
- Minimal downtime database move -> **DMS online migration**, when supported
- Simple database move with acceptable downtime -> **offline migration**
- Supported web application to PaaS -> **App Service Migration Assistant/App Service**
- Petabyte-scale transfer with insufficient bandwidth -> **Data Box**
- Managed migration of on-premises files to Azure Storage -> **Storage Mover**
- Transform data while moving it -> **Data Factory**