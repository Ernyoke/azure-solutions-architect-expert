# Azure MySQL

- It is a managed MySQL instance on Azure
- Works like any other MySQL database using the same tools
- It has great compatibility with on-prem MySQL database
- Offers built-in security, backups, availability and more
- Microsoft manages the OS, MySQL engine, patching, backups and built-in high availability
- Good fit for LAMP/WordPress style applications and lift-and-shift of on-prem MySQL workloads
- No OS level access -> if the workload needs plugins or OS control, use MySQL on an Azure VM (IaaS)

## Deployment Options

- **Single Server**: the original deployment model
    - Retired (end of life September 2024) -> not a valid choice for new designs
    - Fixed maintenance, limited HA control, storage-level replication only
- **Flexible Server**: the current and recommended deployment model
    - Full control over configuration and maintenance window
    - Zone-redundant or same-zone high availability
    - Stop/start the server to save cost on dev/test workloads
    - Private access via VNet integration (no public endpoint at all) or public access
    - Burstable tier for low-cost, intermittent workloads
- Exam rule of thumb: **new MySQL PaaS workload -> Flexible Server**

## Flexible Server Service Tiers

- **Burstable**: B-series compute, accumulates CPU credits
    - Dev/test and workloads that are idle most of the time
    - Does not support high availability or read replicas
- **General Purpose**: balanced compute and memory for most business workloads
- **Business Critical** (previously Memory Optimized): in-memory performance, high concurrency, low latency
    - Supports local SSD cache and the highest IOPS
- Storage is configured independently from compute (up to 16 TB) and supports **storage autogrow**
- Scaling compute up/down requires a restart; **storage can only be increased, never decreased**

## Security

- Includes:
    - IP firewall rules
    - Service Endpoints
    - Private Endpoints
    - Regular and Azure AD Authentication
    - Secure communication using TLS
    - Data is encrypted
- Encryption at rest uses service-managed keys by default; **customer-managed keys (CMK)** in Key Vault are supported
- Networking on Flexible Server:
    - **Private access (VNet integration)**: server is injected into a delegated subnet, no public endpoint -> most secure
    - **Public access**: public endpoint protected by firewall rules, optionally with a Private Endpoint
    - The connectivity method is chosen at creation time and cannot be changed afterwards
- Use Microsoft Entra ID authentication plus **managed identities** so applications avoid storing passwords
- Store any remaining connection strings and passwords in Azure Key Vault

## Backups

- Depends on the service tier:
    - Basic: full backup daily
    - General purpose up to 4 GB:
        - Full backup once a week
        - Differential backup twice a day
        - Transaction log backup every 5 minutes
    - General purpose up to 16 GB:
        - Full backup once a database is created
        - Differential backup once a day
        - Transaction log backup every 5 minutes
- Retention period:
    - We can select between 7 - 35 days (default is 7)
    - No native long term retetion support
- Backups are automatic and enabled by default; there is no extra cost up to the provisioned storage size
- **Point-in-time restore (PITR)** always creates a **new server**; it never overwrites the existing one
- Geo-redundant backup must be enabled at creation time and allows **geo-restore** into the paired region
- For long term retention, export with `mysqldump` or copy the backup into Azure Storage / Azure Backup vault

## Availability

- Backup is stored in a geo-redundant storage for the General and Memory Optimized tiers
- For the basic tiers the backup is stored locally
- SLA: 99.99%

### High Availability (Flexible Server)

- **Zone-redundant HA**: primary and standby in different availability zones of the same region
    - Protects against a zone failure, SLA 99.99%
    - Only in regions that support availability zones
- **Same-zone HA**: primary and standby in the same zone -> lower network latency, no zone protection
    - SLA 99.95%
- Without HA the SLA is 99.9%
- The standby is a hot standby -> **it cannot be used for reads**
- Failover is automatic; applications reconnect using the same server endpoint
- Not supported on the Burstable tier

### Read Replicas

- Up to 10 asynchronous read replicas per server, used for **read scale-out**, not for HA
- Replicas can be created **in another region** -> cross-region disaster recovery and reads close to users
- Replicas are read-only until they are **promoted (stopped)**, which makes them standalone read/write servers
- Because replication is asynchronous, some data loss is possible on failover (RPO > 0)
- Not supported on the Burstable tier

## Migration

- **Azure Database Migration Service (DMS)** supports offline and online (minimal downtime) migrations
- Small databases can be migrated with `mysqldump` / `mysqlpump` or MySQL Workbench
- An online migration can also be done by configuring the Azure server as a replica of the on-prem MySQL (data-in replication)
- Check the engine version and unsupported features/plugins before migrating

## Monitoring

- Metrics, alerts and diagnostic settings are available through Azure Monitor
- Key metrics: CPU percent, memory percent, storage percent, IO percent, active connections, replication lag
- Slow query logs and audit logs can be sent to Log Analytics, Storage or Event Hubs

## Pricing

- Pricing is based on the tier:
    - Basic (for dev and test scenarios)
    - General Purpose (for most business workloads)
    - Memory Optimized (for use-cases that require in-memory performance)
- Other factor of the pricing is the compute power (number of vCores)
- Additionally there is another type of deployment for MySQL called Flexible Server Deployment
- Reservations are applicable for Azure MySQL
- Billed components: compute (vCores), provisioned storage, backup storage beyond the retention allowance and read replicas
- Cost saving options:
    - **Reserved capacity** for 1 or 3 years (up to ~60% saving)
    - **Burstable tier** for dev/test
    - **Stop the server** when it is not used (Flexible Server, stopped up to 30 days)
- There is no Azure Hybrid Benefit for MySQL (it is a SQL Server / Windows licensing benefit)

## Which Option to Choose?

- Need a managed MySQL with HA, VNet integration and cost controls? -> **Flexible Server**
- Need OS access, custom plugins or an unsupported MySQL version? -> **MySQL on Azure VM**
- Need protection against a zone outage? -> **zone-redundant HA**
- Need protection against a region outage? -> **cross-region read replica** or **geo-redundant backup + geo-restore**
- Need to scale reads? -> **read replicas**
- Need global distribution with multi-region writes? -> consider **Azure Cosmos DB**