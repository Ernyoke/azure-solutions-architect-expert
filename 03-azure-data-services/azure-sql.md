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
- Security:
    - IP firewall rules
    - Service Endpoints
    - SQL and Azure AD Authentication
    - Secure communication (TLS)
    - Data encrypted by default (TDE - Transparent Data Encryption)
- Backup:
    - SQL Databases offers the following types of backups:
        - Full -> happens every week
        - Differential -> every 12-24 hours
        - Transaction log ->every 5-10 minutes
    - Retention period for the backups:
        - Regular backups: 7-35 days, configurable, default is 7
        - Long term backups: up to 10 years
- Availability:
    - Backups are stored in a geo-redundant storage
    - Active geo-replication
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
- Allows storing multiple databases on a singler server
- Great for database with low average utilization and infrequent spikes
- It is verry cost effective
- We purchase the compute resourses that we need, not the database

### Managed Instance

- Closer to the on-rem SQL Server compared to Azure SQL Database and Elastic Pool
- Near 100% compatibility with on-prem SQL
- In addition we can deploy managed instance into a VNet
- Business model closer to the on-prem one
- Main differences between Managed Instacne and other flavors:
    - No active geo-replication
    - SLA 99.99%
    - Supports built-in functions
    - Can run CLR code
    - No autoscaling and tuning
    - No availability zone
    - No serverless tier
    - No Hyperscale (specialized tier, offers high performance)

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
        - DTU: 100 DUT ~ 2 vCores
        - vCores
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