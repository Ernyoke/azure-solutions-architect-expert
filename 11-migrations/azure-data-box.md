# Azure Data Box

- Azure service for transferring large amounts of data when network transfer is too slow, expensive or unreliable
- Microsoft ships a secure storage device to the customer, the customer copies data to it, and ships it back for upload into Azure Storage
- Common scenarios include datacenter migration, archival, media libraries, backup seeding and disaster recovery
- Data Box can also export Azure data when supported by the selected service and region

## Data Box Family

| Product | Transfer method | Best for |
| --- | --- | --- |
| Data Box Disk | Microsoft sends encrypted SSDs | Smaller offline transfers and multiple source locations |
| Data Box | Rugged appliance with about 80 TB usable capacity | Large offline transfers into or out of Azure |
| Data Box Heavy | Large appliance with about 770 TB usable capacity | Datacenter-scale and petabyte-scale migrations |
| Data Box Gateway | Customer-provisioned virtual appliance using the network | Ongoing online transfer of files to Azure Storage |

- Device capacity and availability vary by region and order type
- Data Box Gateway is an online virtual appliance, not a shipped offline device

## Import Workflow

1. Create a Data Box order and select the destination storage accounts
2. Microsoft prepares and ships the encrypted device
3. Connect the device to the local network and unlock it with the portal credentials
4. Copy data to the predefined shares using supported protocols
5. Validate the copy and prepare the device for return shipping
6. Microsoft uploads the data to Azure Storage
7. Verify the uploaded data and review copy logs before deleting the source
8. Microsoft securely erases the device after processing

## Destination Services

- Block blobs, page blobs and Azure Files are supported according to the device and order configuration
- Data Lake Storage Gen2 can receive data through its Blob Storage interface
- Choose the destination account, container or share before copying data
- Observe Azure Storage naming, file-size, path-length and object-count limits
- Data Box transfers data; it does not transform schemas or application formats

## Security

- Devices use tamper-resistant enclosures and tamper-evident seals
- Data is encrypted with AES 256-bit encryption
- Device unlock credentials are managed through the Azure portal
- Track the device and shipment through the order resource
- Access to the Data Box order should use least-privilege Azure RBAC
- Microsoft erases the disks according to data-sanitization standards after the job completes

## Planning

- Estimate the data volume, available bandwidth and migration window before choosing offline transfer
- Approximate online transfer time:

$$
\text{transfer time} = \frac{\text{data size in bits}}{\text{effective bandwidth in bits per second}}
$$

- Account for protocol overhead, small-file performance and time required for shipping and Azure ingestion
- Compress large sets of small files when the target workload permits it
- Use checksums and copy logs to validate data integrity
- Freeze writes before the final copy or synchronize changes separately after the bulk seed
- Data Box is not a backup service; configure Azure Backup, redundancy and lifecycle policies after migration

## When to Use

- Large one-time migration with limited or expensive network bandwidth
- Data must be moved without opening a high-bandwidth network connection
- Initial offline seed followed by an incremental online synchronization
- Export data from Azure for compliance, relocation or offline processing when supported

## When Not to Use

- Use **AzCopy** for straightforward online transfers over adequate bandwidth
- Use **Azure Storage Mover** for managed, recurring file migrations
- Use **Data Box Gateway** for continuous online transfer from NFS or SMB shares
- Use **Azure Data Factory** when data must be transformed or orchestrated during movement
- Use **Azure Migrate** for server discovery, assessment and VM migration

## Exam Design Decisions

- Tens of terabytes with insufficient bandwidth -> **Data Box Disk or Data Box**, depending on capacity
- Hundreds of terabytes or petabyte-scale migration -> **Data Box Heavy**
- Continuous network transfer from on-premises file shares -> **Data Box Gateway**
- Fast and reliable network connection available -> **AzCopy or Storage Mover**
- Data transformation required during migration -> **Data Factory**