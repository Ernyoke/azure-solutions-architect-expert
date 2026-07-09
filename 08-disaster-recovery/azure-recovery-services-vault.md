# Azure Recovery Services Vault (ARS)

- It is a storage entity that stores backup copies of data and configuration information over time
- This data can be related to various Azure resources like VMs, workloads, servers or workstations
- Backup for Azure Services: ARS Vault protects data from various Azure services, including Iaas VMs (Linux and Windows), Azure SQL databases, Azure Blob Storages, Azure File shares and Azure Functions
- Recovery services vaults support various platforms:
    - System Center Data Protection Manager (DPM)
    - Windows Servier
    - Integrates wiht Azure Backup Server for application protection
    - Other platforms

## ARS Features

- Enhances Security: ARS encrypts data in transit and at rest for secure backup and restore processes
- Central Monitoring: Azure portal allows centralized monitoring of all backip and restore tasks across hybrid environments
- Azure RBAC: enables granular access control and management of Recovery Services vaults
- Soft Delete: this feature retains backup data for an additional 14 days after deletion, protecting agains accidental or malicious data loss
- Cross Region Restore (CRR): enables data restoration in a secondary region during a disaster in the primary region

## MARS Agent

- Microsoft Azure Recovery Services (MARS) agent can back up files, folders and system state from Windows on-premises machines and Azure VMs
- Backups are stored in a Recovery Services vault in Azure
- MARS agent is also known as the Azure Backup Agent
- MARS agent does not support Linux
- To install the agent and perform backups we need to do the following steops:
    - Create an Azure Recovery Services vault
    - Create a backup policy within the vault
    - Configure security route for backup, e.g. ExpressRoute or Private Endpoints (optional)
    - Download the MARS agent
    - Install and register the agent on our Windows machine

# Azure Backup Policy

- Azure Backup Policy allows users to define and configure how data backups are managed on the Azure platform
- It lets us set the frequency, retention duation and type of backups to ensure data protection and meet organizational requirements
- To create a backup policy we need to do the following:
    - Choose the datasource type:
        - Azure Virtual machines
        - Azure Database for PostgreSQL servers
    - Chose the frequency: daily, weekly, etc.
    - Set the retention snapshot number
    - Chose the time range for retention