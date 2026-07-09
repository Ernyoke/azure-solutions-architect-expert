# Azure SQL

- It is a managed SQL Server on Azure
- Works like any other SQL Server using the same tools
- Offers great compatibility with on-prem SQL Server:
    - Depends on the exact Azure SQL Flavor
- Offers built-in security, backups, availability and more
- Offers flexible pricing models

## Azure SQL Flavors

- There are 3 flavors of Azure SQL:
    - Azure SQL Database
    - Elastic Pool
    - Managed Instance

### Azure SQL Database

- It is a managed SQL Server on Azure
- It is a single database on a single server
- It has automatic backups, updates and scaling
- Good compatibility with on-prem SQL Server, but not all features are supported (such as CLR code)
- Deployment options:
    - Single Database -> dedicated resources for one database
    - Elastic Pool -> shared resources across multiple databases
- Security:
    - IP firewall rules
    - Service Endpoints and Private Endpoints (Private Link)
    - SQL and Microsoft Entra ID (Azure AD) Authentication
    - Secure communication (TLS)
    - Data encrypted at rest by default (TDE - Transparent Data Encryption)
    - Always Encrypted -> protects sensitive data in use (client-side)
    - Microsoft Defender for SQL -> threat detection and vulnerability assessment
- Backup:
    - SQL Databases offers the following types of backups:
        - Full -> happens every week
        - Differential -> every 12-24 hours
        - Transaction log ->every 5-10 minutes
    - Retention period for the backups:
        - Regular backups: 7-35 days, configurable, default is 7
        - Long term backups: up to 10 years
- Availability & Disaster Recovery:
    - Backups are stored in geo-redundant storage by default
    - Active geo-replication -> up to 4 readable secondary replicas, manual failover
    - Auto-failover groups -> automatic failover with a stable connection endpoint (preferred for DR)
    - Zone-redundant configuration -> spreads replicas across Availability Zones
    - SLA: 99.9% - 99.995%, depends on the tier and redundancy
- Compute tiers:
    - Provisioned
        - We pay for allocated resources regardless of actual use
        - Can be reserved, we pay upfront fee for a discount
    - Serverless:
        - We pay only for what we actually use: (vCore + RAM) / second
        - Automatically paused when inactive, in this case we pay only for the storage
        - Slight delay when the database is warming up
        - Cannot be reserved

### Elastic Pool

- Based on Azure SQL
- Allows storing multiple databases on a single server
- Great for databases with low average utilization and infrequent spikes
- Very cost effective -> databases share the same pool of resources (DTUs or vCores)
- We purchase the compute resources for the pool, not per database
- Databases can be moved in and out of the pool

### Managed Instance

- Closer to the on-prem SQL Server compared to Azure SQL Database and Elastic Pool
- Near 100% compatibility with on-prem SQL
- Deployed into a VNet (private IP) -> best for lift-and-shift migrations
- Supports instance-scoped features: SQL Agent, cross-database queries, CLR, Service Broker, Linked Servers
- Business model closer to the on-prem one
- Main differences between Managed Instance and other flavors:
    - Uses auto-failover groups for DR (no active geo-replication of single DBs)
    - SLA 99.99%
    - Supports built-in functions and SQL Agent
    - Can run CLR code
    - No serverless tier (General Purpose only as of GA)
    - No DTU purchase model (vCore only)

## Azure SQL Pricing

- Managed Instance:
    - Service tier:
        - General Purpose
        - Business Critical
    - Instance Type:
        - Instance Pools (available for General Purpose only)
        - Single Instance
    - Instance (vCore): 8 - 80
- Azure SQL
    - Type:
        - Single Database
        - Elastic Pool
    - Purchase Model:
        - DTU -> bundles compute, storage and I/O into a single unit (simple, fixed)
        - vCore -> independently choose compute and storage; supports Azure Hybrid Benefit and reservations
    - Services Tiers:
        - Single Database:
            - DTU:
                - Basic
                - Standard
                - Premium
            - vCores:
                - General Purpose
                - Business Critical
                - Hyperscale
        - Elastic Pool:
            - DTU:
                - Basic
                - Standard
                - Premium
            - vCores:
                - General Purpose
                - Business Critical
    - Compute Tier - for vCore models only:
        - Serverless
        - Provisioned
    - Instance Size - number of vCores/DTUs

## Service Tiers (vCore)

### General Purpose

- Balanced compute and storage; budget-oriented (default choice)
- Available for all deployment options: Single Database, Elastic Pool and Managed Instance
- Storage: remote (Azure Premium) -> higher latency
- Storage limit: up to 4 TB (Single DB / Elastic Pool), up to 16 TB (Managed Instance)
- Availability based on separation of compute and storage (single replica)
- Supports zone-redundant configuration and serverless compute
- SLA: 99.99%

### Business Critical

- High performance, low latency local SSD storage
- Available for all deployment options: Single Database, Elastic Pool and Managed Instance
- Uses Always On availability group with 3 replicas
- Includes a free built-in read-only replica (read scale-out)
- In-Memory OLTP support
- Storage limit: up to 4 TB
- Highest resilience; supports zone-redundancy
- Higher cost than General Purpose
- SLA: 99.99% (up to 99.995% with zone redundancy)

### Hyperscale

- Highly scalable storage up to 100 TB
- Decoupled compute, log and storage architecture
- Fast (near-instant) backups and restores regardless of DB size, based on snapshots
- Rapid horizontal read scale-out -> up to 4 high-availability secondary replicas
- Best for very large databases that need fast scaling
- Single Database only -> not available for Managed Instance or Elastic Pool
- Supports serverless compute

> Note: DTU model has its own tiers -> Basic, Standard, Premium (Single DB and Elastic Pool only).

## Service Tiers (DTU)

- Available for Single Database and Elastic Pool only (not Managed Instance)
- Bundles compute, storage and I/O into a single DTU unit

### Basic

- Lowest cost; for small, infrequently used databases
- Limited storage and performance
- SLA: 99.99%

### Standard

- General-purpose workloads with balanced price/performance
- Wider range of storage and DTU sizes
- SLA: 99.99%

### Premium

- High performance for I/O-intensive, mission-critical workloads
- Uses local SSD storage with built-in replicas (Always On)
- Supports In-Memory OLTP and read scale-out
- SLA: 99.99%

## Which Azure SQL to Choose?

- Are we migrating an on-prem SQL with instance-level features? -> Managed Instance
- Do we need multiple low-utilization DBs? -> Elastic Pool
- Do we need very large databases (>4 TB) with fast scaling? -> Hyperscale
- All other cases -> Azure SQL Database