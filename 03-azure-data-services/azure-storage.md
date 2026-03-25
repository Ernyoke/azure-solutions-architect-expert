# Azure Storage

- It is an object store, used to store objects such as files, documents, videos, images, etc.
- It is massively scalable
- Accessible via HTTP or HTTPS
- We have client libraries for almost every programming languages to access Azure Storage
- Azure Storage is durable and HA

## Azure Storage Types

- Types are:
    - Blobs:
        - Used for object storage
    - Files: 
        - Used for file shares for cloud and on-prem deployments
    - Queues:
        - Used for messaging
    - Tables: 
        - NoSQL data store
        - Similar to CosmosDB, but less optimized for performance and availability
    - Disks: 
        - Storage volumes for Azure VMs
        - They are managed by VMs, from our point of view there is nothign to deal with here

## Azure Blobs Storage

- Blob = Binary Large Object
- Great for storing files, videos, documents, large texts, etc.
- We can upload up to 4.77TB of data per file, 190TB in preview
- Blob storage is extremly cost effective
- It has grear availability options
- It is extremly easy to use
- Usually used in conjunction with SQL/NoSQL databases

## Azure Blon Storage Security

- Includes:
    - IP firewall rules
    - Service Endpoints
    - Private Endpoints
    - Shared Access Signatures
    - Access Keys and Azure AD Authentication
    - Secure communication using TLS
    - Data is encrypted by default

## Azure Blob Storage Structure

- Account -> Container -> Blobs

## Azure Blob Storage Redundancy

- There are 6 options:
    - Locally Redundant Storage (LRS):
        - Data is synchronously copied 3 times within the same AZ (same datacenter)
    - Zone Redundant Storage (ZRS):
        - Data is synchronously copied 3 times within the zones in the region
        - In case there is only one Az in the region ZRS will not be available
    - Geo Redundant Storage (GRS):
        - Data is synchronously copied 3 times within the same zone, and then copied asynchronously to the paired region
        - Data in the secondary region is accessible after failover process
    - Geo-Zone Redundant Storage (GZRS):
        - Combination of GRS and ZRS
        - Data is synchronously copied 3 times within the zones in the region, and then copied asynchronously to the paired region
        - Data in the secondary region is accessible after failover process
    - Read Access-Geo Redundant Storage (RA-GRS):
        - Similar to GRS, but the secondary region has read access to the data
    - Read Access-Geo-Zone Redundant Storage (RA-GZRS):
        - Similar to GZRS, but the secondary region has read access to the data

## Azure Blob Storage Failover

- In case a region failure, replication cannot happen and the failover can be initiated to the secondary region
- Failover can be initiated by:
    - Portal
    - Azure CLI
    - PowerShell

## Azure Blob Storage Tiers

- Tiers are:
    - Hot:
        - Targeted for data the needs to be accessed frequently
        - Best SLA: 99.9%
        - Has the highest storage cost, but he lowest access cost
        - Recommended for:
            - Photos to be displayed on a site
            - Documetns to be donwloaded
            - etc.
    - Cool:
        - Recommeded for data that we need to access infrequently
        - Slightly lower SLA: 99%
        - It has lower storage costs, but access costs are higher tha the Hot tier
        - Data must be stored for at least 30 days, otherwise early fees are applied for deletion
        - Examples of usage:
            - Short term backups
            - Data for future processing
    - Archive:
        - Targeted for data archival
        - Data is stored offline => no SLA
        - Time to retrieve data can take hours
        - Has the lowest storage costs, but also has the highest access costs
        - Data must be stored for at least 180 days, otherwise early fees are applied for deletion
- Retrieval time is the same in case of the Hot and Cool tiers
- Archive tier does not support ZRS, GRS and RA-GRS redundancy
- Using RA-G(Z)RS, SLA improves to 99.99% (Hot) and 99.9% (Cool)
- Tier is set at the account level, but can be modified by blob
- Moving between tiers can be automated by lifecycle rules
