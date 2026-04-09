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

## Azure Blob Storage Pricing

- Pricing is based on:
    - Redudancy options
    - Access tier: hot, cool, archive
    - Capacity: storage used
    
## Soft Delete

- It is enabled by default when a storage account is created
- It can be enabled for blobs, containers and file shares
- Soft delete lets us "un-delete" a resource in case it was deleted by mistake
- Soft delete has a retention period (7 days by default)

## Azure Blob Container Access Level

- When creating a container we can select the access level for it:
    - Private: no anonymous access
    - Blob: anonymous read access for blobs only
    - Container: anonymous read access for containers and blobs - we can view the full list of blobs in a container

## Accessing Data from a Storage Account

- Account access key: 
    - Requires that we have an Azure role assigned to our user
    - The role should have the `Microsoft.Storage/storageAccounts/listkeys/action` RBAC action
- Access keys:
    - We can create access keys with connections strings to authenticate within a container and access the content
    - Used for client libraries and programmatic access
- Shared access signature (SAS token):
    - We can provide limited access to containers within our account by generating an URI
    - With shared access signature we can provide fined grained access to containers, files, queues and tables
    - Shared access signatures have an expiry date (default 8 hours)
    - SAS token can limit access to specific IP addresses

## Storage Capacity

- We can view how much storage we use for a storage account under `Insights`
- Capacity number is updating on an eventual consistency basis

## Storage Account Networking

- We can limit access to specific VNets for a storage account
- We can completly disable public access to storage accounts
- Private endpoint connections are supported for storage accounts

## Storage Account Redundancy

- Failover between the primary and secondary regions is automatic, but we can trigger it manually

## Front Door and CDN

- We can define a CDN endpoint for our storage account
- The purpose of a CDN is to bring the content of the storage account closer to the consumer
- Currently storage accounts can use Front Door and Azure CDN to setup replication to the edge locations