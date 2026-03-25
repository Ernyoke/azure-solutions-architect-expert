# Azure MySQL

- It is a managed MySQL instance on Azure
- Works like any other MySQL database using the same tools
- It has great compatibility with on-prem MySQL database
- Offers built-in security, backups, availability and more

## Security

- Includes:
    - IP firewall rules
    - Service Endpoints
    - Private Endpoints
    - Regular and Azure AD Authentication
    - Secure communication using TLS
    - Data that is encrypted

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

## Availability

- Backup is stored in a geo-redundant storage for the General and Memory Optimized tiers
- For the basic tiers the backup is stored locally
- SLA: 99.99%

## Pricing

- Pricing is based on the tier:
    - Basic (for dev and test scenarios)
    - General Purpose (for most business workloads)
    - Memory Optimized (for use-cases that require in-memory performance)
- Other factor of the pricing is the compute power (number of vCores)
- Additionally there is another type of deployment for MySQL called Flexible Server Deployment
- Reservations are applicable for Azure MySQL