# Azure Storage

- It is an object store, used to store objects such as files, documents, videos, images, etc.
- It is massively scalable
- Accessible via HTTP or HTTPS
- We have client libraries for almost every programming language to access Azure Storage
- Azure Storage is durable and HA
- Forms the storage backbone for many Azure services (VM disks, diagnostics, boot diagnostics, Azure Functions, etc.)

## Azure Storage Accounts

- Azure Storage offers several types of storage accounts (Account Kind):
    - Standard general purpose v1 (legacy)
    - Standard general purpose v2
    - BlobStorage (legacy)
    - Premium block blobs
    - Premium file shares
    - Premium page blobs
- Storage accounts vary with the following features:
    - Supported Services: what can we put in a storage account?
        - Blob, File, Queue, Table, Disk and Data Lake Gen2
    - Performance Tiers: how fast will we read and write
        - **Standard** (HDD-backed, lowest cost, general purpose)
        - **Premium** (SSD-backed, low latency; uses LRS/ZRS only; chosen via specialized account kinds: BlockBlobStorage, FileStorage, page blobs)
    - Access Tiers: how often we need quick access to files
        - Hot, Cool, Cold, Archive
    - Replication:
        - LRS, GRS, RA-GRS, ZRS, GZRS, RA-GZRS
    - Deployment model:
        - Resource Manager, Classic
- For most new workloads **Standard general purpose v2** is the recommended account kind

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
        - With managed disks Azure handles the underlying storage account; from our point of view there is nothing to manage here
    - Data Lake Storage Gen2:
        - Blob storage with a **hierarchical namespace** enabled
        - Optimized for big data analytics workloads (HDFS-compatible)

## Azure Blobs Storage

- Blob = Binary Large Object
- Great for storing files, videos, documents, large texts, etc.
- We can upload up to 4.77TB of data per file, 190TB in preview
- Blob storage is extremely cost effective
- It has great availability options
- It is extremely easy to use
- Usually used in conjunction with SQL/NoSQL databases
- Three blob types:
    - **Block blobs**: text and binary data (files, images, documents) - the most common type
    - **Append blobs**: optimized for append operations (e.g., logging)
    - **Page blobs**: random read/write, back **unmanaged** VM disks (up to 8 TB)

## Azure Blob Storage Security

- Includes:
    - IP firewall rules
    - Service Endpoints
    - Private Endpoints
    - Shared Access Signatures
    - Access Keys and Microsoft Entra ID Authentication
    - Secure communication using TLS
    - Data is encrypted by default
- **Encryption at rest (Storage Service Encryption, SSE)**:
    - Always on, cannot be disabled, uses 256-bit AES
    - **Microsoft-managed keys (MMK)** by default
    - **Customer-managed keys (CMK)** in Azure Key Vault for control over rotation/lifecycle
    - **Customer-provided keys** can be supplied per blob request
    - **Infrastructure encryption** can be enabled for a second layer (double encryption)
    - **Encryption scopes** allow different keys for different containers/blobs within one account

## Azure Blob Storage Structure

- Account -> Container -> Blobs

## Azure Blob Storage Redundancy

- There are 6 options:
    - Locally Redundant Storage (LRS):
        - Data is synchronously copied 3 times within the same AZ (same datacenter)
    - Zone Redundant Storage (ZRS):
        - Data is synchronously copied 3 times within the zones in the region
        - In case there is only one AZ in the region ZRS will not be available
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
        - Targeted for data that needs to be accessed frequently
        - Best SLA: 99.9%
        - Has the highest storage cost, but the lowest access cost
        - Recommended for:
            - Photos to be displayed on a site
            - Documents to be downloaded
            - etc.
    - Cool:
        - Recommended for data that we need to access infrequently
        - Slightly lower SLA: 99%
        - It has lower storage costs, but access costs are higher than the Hot tier
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
- Account Level Tiering: any blob that does not have an explicit tier assigned infers the tier from the Storage Account access tier setting
- Blob-level tiering: 
    - We can upload a blob to the tier of our choice
    - Changing tiers happens instantly with exception from moving out of archive
- Rehydrating a Blob: when moving a blob out of archive into another tier it can take several hour => this is called "rehydrating"
- When a blob is uploaded or moved to another tier, it is charged immediately upon tier change:
    - When moving from a cooler tier:
        - The operation is billed as a write operation to the destination tier
        - Write operation (per 10_000) and data write (per GB) charges of the destination tier apply
    - When moving from a hotter tier:
        - The operation is billed as a read from the source tier
        - Read operation (per 10_000) and data retrieval (per GB) charges of the source tier apply
        - Early deletion charges from any blob moved out of the cool/archive tier may apply as well
- There is also a **Cold** tier (between Cool and Archive): online access, lower storage cost than Cool, 90-day minimum, higher access cost

## Azure Blob Storage Pricing

- Pricing is based on:
    - Redundancy options
    - Access tier: hot, cool, cold, archive
    - Capacity: storage used
    - Operations and data transfer (read/write/list, egress)
- **Reserved capacity** (1 or 3 years) provides discounts for committed Block Blob/Data Lake storage

## Lifecycle Management

- Rule-based policies to automatically transition or delete blobs based on age/last modified/last accessed
- Typical actions:
    - Move to Cool/Cold/Archive after N days
    - Delete blobs (or previous versions/snapshots) after N days
- Helps optimize cost by moving cold data to cheaper tiers automatically

## Data Protection Features

- **Blob versioning**: automatically keeps previous versions of a blob on overwrite/delete
- **Snapshots**: read-only point-in-time copies of a blob (manual)
- **Soft delete** (blobs, containers, file shares): recover deleted data within a retention window
- **Change feed**: ordered, durable log of all create/update/delete changes to blobs
- **Point-in-time restore** for block blobs (requires versioning + change feed + soft delete)
- **Immutable storage (WORM)** for compliance:
    - **Time-based retention** policies and **legal holds**
    - Data cannot be modified or deleted while the policy is active
- **Object replication**: asynchronously copies block blobs from a source to a destination account (requires versioning + change feed)

## Static Website Hosting

- Blob storage can host **static websites** directly (HTML, CSS, JS) from the special `$web` container
- Serves content from a primary endpoint; combine with **Azure Front Door / CDN** for HTTPS on custom domains and caching

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
    - SAS token can limit access to specific IP addresses and protocols (HTTPS only)
    - SAS types:
        - **Account SAS**: access to one or more services at the account level
        - **Service SAS**: access to a resource in a single service (e.g., a blob/container)
        - **User delegation SAS**: secured with **Microsoft Entra ID** credentials instead of the account key (most secure, recommended for Blob)
    - **Stored access policies** allow centrally managing/revoking service SAS tokens
- Authorization options summary: **anonymous public access**, **Shared Key (account keys)**, **SAS tokens**, and **Microsoft Entra ID (RBAC)** - Entra ID is the recommended approach
- Use **Azure Key Vault** to store and rotate account keys/connection strings

## Storage Capacity

- We can view how much storage we use for a storage account under `Insights`
- Capacity number is updating on an eventual consistency basis

## Storage Account Networking

- We can limit access to specific VNets for a storage account
- We can completly disable public access to storage accounts
- Private endpoint connections are supported for storage accounts

## Storage Account Redundancy

- Failover between the primary and secondary regions is automatic, but we can trigger it manually

## AzCopy

- AzCopy is a command-line utility that we can use to copy blobs or files to/from a storage account
- To download data we need Storage Blob Data Reader access
- To upload data:
    - Storage Blob Data contributor
    - Storage Blob Data Owner

## Front Door and CDN

- We can define a CDN endpoint for our storage account
- The purpose of a CDN is to bring the content of the storage account closer to the consumer
- Currently storage accounts can use Front Door and Azure CDN to setup replication to the edge locations
- Azure Front Door is a comprehensive, modern application delivery platform (CDN + WAF + Load Balancer) optimized for dynamic content, APIs, and global application security. Azure CDN is a simpler, specialized service primarily optimized for caching and delivering static content (images, videos, files)

## Azure Files

- Fully managed **file shares** in the cloud accessed via **SMB**, **NFS**, and the FileREST API
- Can be **mounted** concurrently by cloud VMs and on-prem machines (cross-platform)
- Common use cases: lift-and-shift of on-prem file shares, shared application config/tools, replacing file servers
- Authentication/authorization via **identity-based access** (on-prem AD DS or **Microsoft Entra Domain Services**) or the storage account key
- Tiers: Premium (SSD, FileStorage account) and Standard (transaction-optimized, Hot, Cool)
- **Azure File Sync**: caches Azure file shares on on-prem Windows Servers (cloud tiering) for fast local access with the cloud as the source of truth

## Azure Data Lake Storage Gen2

- Built on Blob storage with a **hierarchical namespace (HNS)** enabled (true directories, atomic folder operations)
- HDFS-compatible; designed for **big data analytics** (Azure Synapse, Databricks, HDInsight)
- Supports POSIX-like **ACLs** in addition to Entra ID RBAC for fine-grained access
- Inherits Blob features: tiers, lifecycle management, redundancy options