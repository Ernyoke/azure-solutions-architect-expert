# Azure SQL

- Azure SQL is a family of Azure services based on the Microsoft SQL Server database engine
- The services share familiar SQL Server tools and the T-SQL language, but differ in compatibility and how much infrastructure Microsoft manages
- Azure SQL provides built-in patching, backups, high availability, monitoring, and security capabilities

## Azure SQL Service Map

- **Azure SQL Database**: fully managed platform as a service (PaaS) for modern cloud applications
    - Deploy as a single database or place multiple databases in an elastic pool
    - Best when applications can work at database scope and do not require full SQL Server instance compatibility
- **Azure SQL Managed Instance**: fully managed PaaS with near-full SQL Server instance compatibility
    - Best for migrations that require instance-scoped features with minimal application changes
- **SQL Server on Azure Virtual Machines**: infrastructure as a service (IaaS) with full SQL Server and operating system control
    - Best when the workload requires OS access, unsupported SQL Server features, or a specific SQL Server version
- **Elastic pool is not a separate service**: it is a purchasing and resource-sharing option for multiple Azure SQL databases

## Azure SQL Database

- A fully managed database service where Microsoft manages the database engine, operating system, patching, backups, and built-in availability
- Databases are associated with a **logical server**, which is an administrative container for logins, firewall rules, auditing, and policies; it is not a physical SQL Server
- Supports most database-scoped SQL Server features, but not every instance-scoped feature
- Choose Azure SQL Database for new cloud applications, isolated databases, and applications designed to scale at database level
- Deployment options:
    - **Single database**: one database receives its own purchased compute resources
    - **Elastic pool**: multiple databases share a purchased pool of compute resources

## Azure SQL Database - Elastic Pools

- An elastic pool contains multiple Azure SQL databases on the same logical server that share DTU-based or vCore-based compute resources
- Choose an elastic pool when databases have variable and unpredictable usage peaks that occur at different times
- The pool sets total resources, while optional per-database minimums and maximums prevent one database from consuming the entire pool
- Cost-effective for software as a service (SaaS) and multitenant designs with many databases that have low average utilization
- Avoid an elastic pool when each database has consistently high or predictable utilization; dedicated single-database resources can be easier to size
- Databases can be moved into and out of a pool

## Azure SQL Managed Instance

- A fully managed SQL instance with near-full compatibility with the SQL Server database engine
- Supports instance-scoped features such as SQL Server Agent, cross-database queries, CLR, Service Broker, and linked servers
- Deployed into a dedicated subnet in an Azure virtual network and accessed through private IP addresses by default
- Choose Managed Instance for lift-and-shift migrations that need instance-level features but do not require operating system access
- Uses the vCore purchasing model and supports General Purpose and Business Critical service tiers
- Does not support the DTU purchasing model or serverless compute
- Uses failover groups for cross-region disaster recovery; active geo-replication is an Azure SQL Database feature
- Choose SQL Server on Azure VMs instead when the workload requires OS access, file-system access, or unsupported SQL Server features

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

## DTU vs vCore Purchasing Model

- The purchasing model determines how Azure SQL compute resources are selected and billed; it does not change the SQL language or application compatibility
- **DTU (Database Transaction Unit)** bundles CPU, memory, data I/O, and transaction log I/O into one performance unit
    - Choose DTU for small or straightforward Azure SQL Database workloads when simple bundled sizing is more important than resource transparency
    - Useful when existing DTU monitoring or benchmarks already provide a known size
    - Available only for Azure SQL Database single databases and elastic pools
    - Uses the Basic, Standard, and Premium service tiers
- **vCore** exposes the number of virtual cores and lets compute generation, service tier, storage, and compute tier be selected more independently
    - Choose vCore for most new production workloads because sizing and costs are easier to compare with on-premises hardware
    - Choose vCore when Azure Hybrid Benefit, reserved capacity, serverless compute, or independently configurable storage is required
    - Required for Azure SQL Managed Instance and Hyperscale
    - Uses the General Purpose, Business Critical, and Hyperscale service tiers
- For migrations, use the Azure SQL migration tools or the DTU calculator to estimate the required size from CPU, memory, I/O, and storage measurements
- A database can generally be converted between compatible DTU and vCore tiers, so the initial choice does not permanently lock the database to one model
- Exam rule of thumb:
    - **Simplicity and a small predictable workload** -> DTU
    - **Cost transparency, flexibility, production optimization, or SQL Server license benefits** -> vCore
    - **Managed Instance, Hyperscale, or serverless** -> vCore is required

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