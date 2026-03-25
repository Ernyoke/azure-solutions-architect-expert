# Azure PostgreSQL

- It is managed PostgreSQL on Azure
- Works like any other PostgreSQL database instance on-prem using the same tools
- Has great compatibility with on-prem PostgreSQL database
- Includes Hyperscale deployment
- Azure PostgreSQL offers built-in security, backups, availability and more

## Security

- Includes:
    - IP firewall rules
    - Service Endpoints
    - Private Endpoints
    - Regular and Azure AD Authentication
    - Secure communication using TLS
    - Data is encrypted

## Backups

- Depends on the storage size:
    - Up to 4 GB:
        - Full backup once a week
        - Differential backup twice a day
        - Transaction log backup every 5 minutes
    - Retention period:
        - 7-35 days (default is 7)
        - There is no native offering for long term retention support
    - Availability:
        - Backups are stored in a geo-redundanbt storage for General Purpose and Memory Optimized tiers
        - SLA: 99.99%
