# Databases on VMs

- An Azure VM can be setup with database software
- There are ready-mdate VMs in the marketplace with preinstalled database software
- Pros of database on VMs:
    - Full flexibility
    - Full control
- Cons of databases on VMs:
    - We have to take care of everything:
        - SLA
        - Updates
        - Availability
        - Security

## High Availability for SQL Server on VMs

- With IaaS we are responsible for HA => we have to build it ourselves with SQL Server Always On features
- Both options require a **Windows Server Failover Cluster (WSFC)** and VMs in an availability set or across availability zones
- VM SLA still applies: 99.95% (availability set), 99.99% (availability zones)

### Always On Failover Cluster Instance (FCI)

- Protects the **whole SQL Server instance** (all databases, logins, SQL Agent jobs, linked servers)
- There is only **one copy of the data** on **shared storage**
- Shared storage options in Azure:
    - Azure shared disks (Premium SSD)
    - Premium file shares
    - Storage Spaces Direct (S2D)
- Only one node is active, the others are passive => no readable secondary
- Protects against **node/OS failure**, but **not against storage failure** (single copy of data)
- Failover is slower: the instance starts up and databases go through recovery
- Cross-region DR is not practical (shared storage is region bound)
- Clients connect through the **Virtual Network Name (VNN)** or a **Distributed Network Name (DNN)**

### Always On Availability Group (AG)

- Protects a **group of databases**, not the entire instance
- Every replica has its **own copy of the data** => no shared storage needed
- Up to 1 primary + 8 secondary replicas
- Replication modes:
    - **Synchronous commit**: no data loss (RPO = 0), used for HA inside a region
    - **Asynchronous commit**: possible data loss, used for DR across regions
- Secondaries can be **readable** => read-scale and backup offloading
- Protects against **node and storage failure**
- Supports **cross-region DR** (async replicas, distributed AGs)
- Clients connect through the **AG Listener**

### FCI vs AG - Exam Cheatsheet

| Aspect | FCI | AG |
|---|---|---|
| Scope | Whole instance | Selected databases |
| Data copies | One (shared storage) | One per replica |
| Shared storage | Required | Not needed |
| Readable secondary | No | Yes |
| Storage failure protection | No | Yes |
| Cross-region DR | No | Yes |
| Failover speed | Slower | Faster |
| Connection endpoint | VNN / DNN | AG Listener |

- Keywords to look for in exam questions:
    - "instance-level objects", "SQL Agent jobs must fail over", "shared storage" => **FCI**
    - "read-only replicas", "offload reporting/backups", "another region", "no data loss" => **AG**
    - "minimal administration", "PaaS", "no OS management" => not a VM at all, use **Azure SQL Database / Managed Instance**
- An FCI can also be used as a replica inside an AG

### Azure Specific Requirements

- Azure networking does not support floating IPs / gratuitous ARP, so the listener needs either:
    - **Azure Load Balancer** (internal) with a health probe and floating IP (direct server return) enabled, or
    - **Distributed Network Name (DNN)** - the simpler and recommended modern option
- Use the **SQL Server IaaS Agent extension** for automated patching, automated backups and Entra ID based Key Vault integration
- **Azure Hybrid Benefit** can be used to reuse existing SQL Server licenses with Software Assurance