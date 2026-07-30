# Azure Backup Service

- It is a cloud-based service that provides secure, scalable backup and recovery for Azure and on-premises workloads
- Key components of Azure Backup include:
    - MARS agent (Microsoft Azure Recovery Services agent)
    - Recovery Services vault and Backup vault
    - MABS (Microsoft Azure Backup Server)
    - Azure VM backup extension
    - Backup policies
- What can be backed up:
    - Files, folders and system state on supported Windows machines
    - Azure VMs
    - Azure managed disks
    - Azure Files shares
    - SQL Server and SAP HANA databases running in Azure VMs
    - Azure Database for PostgreSQL Flexible Server
    - Azure Blobs
- Why use Azure Backup?
    - Offload on-premises backups
    - Back up Azure IaaS VMs
    - Transfer backup data to Azure without ingress charges; restore and cross-region operations can incur charges
    - Keep data secure
    - Centralize monitoring and management
    - Create application-consistent backups for supported workloads
    - Automatic storage management
    - Use locally redundant or geo-redundant vault storage

## Recovery Services Vault

- A Recovery Services vault stores backup data and recovery points for supported workloads
- It supports Azure VMs, Azure Files, SQL Server and SAP HANA in Azure VMs, and on-premises workloads protected by MARS, MABS or DPM
- Azure managed disks, Azure Blobs and PostgreSQL Flexible Server use a Backup vault instead
- Recovery Services vaults support:
    - System Center Data Protection Manager (DPM)
    - Windows Server and Windows clients through the MARS agent
    - Microsoft Azure Backup Server (MABS)
- Key features:
    - Encryption in transit and at rest
    - Centralized monitoring of backup and restore jobs in the Azure portal
    - Azure RBAC for granular access control
    - Soft delete to retain deleted backup data for a configurable period and protect against accidental or malicious deletion
    - Cross Region Restore (CRR) for supported workloads when the vault uses geo-redundant storage and CRR is enabled

## MARS Agent

- The Microsoft Azure Recovery Services (MARS) agent backs up files, folders and system state from supported Windows machines
- Backups are stored in a Recovery Services vault in Azure
- The MARS agent is also known as the Azure Backup agent
- The MARS agent does not support Linux or application-aware workload backups
- To install the agent and perform backups:
    - Create a Recovery Services vault
    - Download the MARS agent
    - Download the vault credentials file
    - Install and register the agent on the Windows machine
    - Configure the backup schedule and retention in the MARS console

## System Center Data Protection Manager (DPM)

- DPM is the enterprise backup component of Microsoft System Center, installed on an on-premises (or Azure VM) Windows Server
- It provides **application-aware**, disk-to-disk-to-cloud backup and is used when the MARS agent alone is not enough
- Requires a **System Center license**
- What DPM can protect:
    - Files, folders, volumes and Windows **system state / bare-metal recovery**
    - **Hyper-V** and **VMware** virtual machines
    - Application workloads: **SQL Server, Exchange, SharePoint**
    - Windows and Linux guest workloads running on supported hypervisors
- Backup targets (three tiers):
    - **Local disk (storage pool)**: fast, short-term operational recovery
    - **Tape**: long-term retention and offsite archival (DPM only)
    - **Azure Recovery Services vault**: long-term retention in the cloud
- How DPM integrates with Azure Backup:
    - A protection agent is installed on each protected server and sends data to the DPM server
    - The **MARS agent** is installed on the **DPM server**, which then sends the backup data to a Recovery Services vault
    - Protected servers never talk to Azure directly - only the DPM server does
- Benefits:
    - Short RTO for local restores, plus offsite copies in Azure without a secondary datacenter
    - Centralized protection for many servers with a single connection point to Azure
    - Continues to work when the Azure connection is temporarily unavailable, because backups land on local disk first

### DPM vs MABS

- **MABS (Microsoft Azure Backup Server)** is essentially DPM without the System Center dependency
- **License**: DPM requires System Center; MABS is included with Azure Backup at no extra license cost (only vault storage is billed)
- **Tape**: DPM supports tape backup; **MABS does not**
- **Azure requirement**: MABS must be registered with a Recovery Services vault; DPM can run standalone without Azure
- Both use the same engine, protection agents, and workload support, and both use the MARS agent to send data to Azure
- Rule of thumb:
    - Already have System Center or need **tape** -> **DPM**
    - Need application-aware on-premises backup with Azure long-term retention, no System Center -> **MABS**
    - Only files, folders and system state on a few Windows machines -> **MARS agent alone**

## Azure Backup Policy

- An Azure Backup policy defines when backups run and how long recovery points are retained
- Policies are workload-specific; available schedule and retention options vary by data source and policy type
- To create a backup policy:
    - Select the data source type, such as Azure Virtual Machine or Azure Database for PostgreSQL Flexible Server
    - Choose the backup frequency and schedule
    - Configure retention for daily, weekly, monthly and yearly recovery points as supported
    - Associate the policy with the resources to protect

## Azure VM Backup

- Azure VM Backup is a solution for backing up and restoring VMs running on Azure
- It protects VM data against accidental deletion, hardware failure, ransomware and other forms of data loss
- How it works:
    - Azure VM Backup provides centralized management through the Azure portal, Azure CLI, PowerShell and REST APIs
    - It supports both Windows and Linux VMs
    - The first backup is a full backup; subsequent backups are incremental
    - Backup policies retain recovery points for specified periods to meet data-management and compliance requirements
- Key features:
    - Application-consistent snapshots for supported workloads through the VM backup extension
    - Encryption of backup data in transit and at rest
    - Restore options include a complete VM, disks or individual files
    - Recovery points are stored in a Recovery Services vault
- Azure Backup is designed for backup and restore and typically has a higher RPO and RTO than replication-based disaster recovery
- Use Azure Site Recovery to replicate VMs to another region and orchestrate failover and failback