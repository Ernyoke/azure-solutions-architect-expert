# Storage Account

- Used to store almost anything in Azure
- Used transparently by various services to store things
- For example, it can be used to store:
    - Database backups
    - VM Disks
    - Diagnostics data
- It can be also used for explicit data storage
- It is cheap for storing data

## Storage Account Naming

- The name must be **globally unique** (it becomes part of the endpoint URL)
- 3-24 characters, **lowercase letters and numbers only** (no hyphens)
- Endpoints follow the pattern:
    - Blob: `https://<account>.blob.core.windows.net`
    - File: `https://<account>.file.core.windows.net`
    - Queue: `https://<account>.queue.core.windows.net`
    - Table: `https://<account>.table.core.windows.net`
    - Data Lake Gen2: `https://<account>.dfs.core.windows.net`

## Data Services in a Storage Account

- A single storage account can contain:
    - **Blobs**: object storage for files, images, videos, backups
    - **Files**: managed SMB/NFS file shares
    - **Queues**: simple messaging store
    - **Tables**: NoSQL key-value store
    - **Disks**: storage volumes for VMs (managed disks hide the account from us)

## Key Settings Chosen at Creation

- **Account kind**: Standard general purpose v2 is the default recommendation
- **Performance**: Standard (HDD-backed) or Premium (SSD-backed, low latency)
- **Redundancy**: LRS, ZRS, GRS, GZRS, RA-GRS, RA-GZRS
- **Access tier** (blobs): Hot, Cool, Cold, Archive
- Region: a storage account lives in a **single region** and inside a **resource group**

## Access and Security Basics

- Access options:
    - **Access keys**: two keys per account, full control, can be rotated
    - **Shared Access Signature (SAS)**: time and permission limited, delegated access
    - **Microsoft Entra ID + RBAC**: the recommended option, no secrets to manage
- Data is **encrypted at rest by default** (256-bit AES, cannot be disabled)
- Traffic can be restricted with firewall rules, service endpoints or private endpoints
- "Secure transfer required" enforces HTTPS

## Limits and Cost

- Default limit of **250 storage accounts per region per subscription**
- Max capacity of a single account: **5 PiB**
- Cost drivers: capacity used, redundancy option, access tier, operations and egress
- No charge for creating the account itself, we only pay for what is stored and used

> For a deeper dive see [03-azure-data-services/azure-storage.md](03-azure-data-services/azure-storage.md)