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