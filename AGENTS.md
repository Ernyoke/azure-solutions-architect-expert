# AGENTS.md

## Repository Purpose

This is a documentation repository containing study notes for the **Azure Solutions Architect Expert (AZ-305)** certification exam. The content is organized as a structured learning resource covering Azure services, concepts, and best practices.

## Repository Structure

The repository follows a numbered directory structure representing different topics:

- `00-basic-concepts/` - Foundational Azure concepts (regions, resource groups, cost, SLA, storage)
- `01-azure-compute/` - Azure compute services (overview, VMs, VM Scale Sets, App Service, Azure Functions, AKS)
- `02-azure-networking/` - Azure networking services (Virtual Networks, Load Balancer, Application Gateway)
- `03-azure-data-services/` - Azure data services (Azure SQL, Databases on VMs, Azure MySQL, Azure PostgreSQL, Cosmos DB, Azure Redis, Azure Storage, Event Hubs, Service Bus)
- `04-messaging-in-azure/` - Azure messaging services (Event Grid, Storage Queue)
- `05-identity-management/` - Identity and access management (Microsoft Entra ID)

Each directory contains markdown files covering specific topics within that category.

## Content Organization Principles

1. **Hierarchical Learning Path**: Topics are numbered to indicate learning sequence (00, 01, 02, etc.)
2. **One Topic Per File**: Each markdown file focuses on a single concept or service
3. **Structured Format**: Notes follow a consistent structure:
   - Main concept definition
   - Key characteristics and use cases
   - Specific details (pricing, availability, configuration options)
   - Best practices and naming conventions where applicable

## Naming Conventions

- **Resource Groups**: Should include `rg` or `RG` as prefix or suffix
- **Files**: Use lowercase with hyphens (e.g., `resource-group.md`, `vm.md`)
- **Directories**: Use numbered prefixes with descriptive names (e.g., `00-basic-concepts`)

## Key Azure Concepts Covered

### Regions and Availability
- **Regions**: Geographic areas with multiple data centers
- **Availability Zones**: Physically separate data centers within a region (best SLA)
- **Fault Domain**: Physical hardware sharing common power (rack level)
- **Update Domain**: Hardware that can be rebooted simultaneously for maintenance
- **Availability Set**: Spread VMs across up to 3 Fault Domains and 20 Update Domains (single AZ)

### Cost Management
- Always verify costs before provisioning resources
- Consider cost-effective alternatives (e.g., Standard SSD vs Premium SSD)
- Use Azure Pricing Calculator: https://azure.microsoft.com/en-us/pricing/calculator/
- Leverage cost-saving strategies: Reserved Instances, Spot Instances, Auto-shutdown

### SLA Calculations
- Combined SLA = Product of individual service SLAs
- Example: 99.95% × 99.99% = 99.94% actual SLA

### Resource Management
- **Resource Groups**: Logical containers for organizing resources in a subscription
- **Naming Conventions**: Use `rg` or `RG` prefix/suffix for resource groups
- **Management Hierarchy**: Subscriptions → Management Groups → Resource Groups → Resources

### Compute Services
- **Compute Overview**: 4 types of compute services (VMs, App Services, AKS, Azure Functions) with varying control and flexibility
- **Virtual Machines**: IaaS compute with various sizes and OS options
- **VM Scale Sets**: Auto-scaling groups for VMs
- **App Service**: Fully managed web hosting with deployment slots and auto-scaling
- **Azure Functions**: Serverless compute for event-driven workloads
- **AKS**: Managed Kubernetes container orchestration

### Storage
- **Storage Accounts**: Highly available, scalable cloud storage (Blob, Queue, Table, File)

### Networking
- **Virtual Networks (VNets)**: Isolated network environments with subnets, address spaces, and CIDR notation
- **Network Security Groups (NSGs)**: 5-tuple filtering with priority-based security rules and Service Tags
- **Service Endpoints**: Securely route traffic to managed services over Azure backbone (free)
- **Private Link**: Extend managed services into VNet with private IP; supports on-prem connectivity (paid)
- **Secure VM Access**: JIT Access, VPN, Jump Box, Bastion
- **VNet Peering**: Direct connectivity between virtual networks
- **Load Balancer**: Layer 4 load balancing with 5-tuple hash; Basic vs Standard tiers; health probes (TCP/HTTP/HTTPS)
- **Application Gateway**: Layer 7 load balancing with URL-based routing, SSL termination, session affinity, and autoscaling
- **Web Application Firewall (WAF)**: Protection against XSS, SQL injection using OWASP ruleset; Detection and Prevention modes
- **Application Gateway Ingress Controller (AGIC)**: AKS integration for Application Gateway

### Data Services
- **Databases on VMs**: Full control IaaS approach with prebuilt marketplace images; user manages SLA, updates, availability
- **Azure SQL Database**: Managed single database with automatic backups, TDE encryption, IP firewall, geo-replication (SLA 99.9%-99.995%)
- **Elastic Pool**: Multiple databases sharing compute resources on a single server; cost-effective for low-utilization workloads
- **Managed Instance**: Near 100% on-prem SQL Server compatibility; VNet deployment; supports CLR code; no serverless tier
- **Azure SQL Pricing**: DTU vs vCore purchase models; General Purpose, Business Critical, Hyperscale service tiers; Provisioned vs Serverless compute
- **Azure MySQL**: Managed MySQL with IP firewall, Service/Private Endpoints, Azure AD auth, TLS; tiered backups (Basic, General Purpose); SLA 99.99%; Basic/General Purpose/Memory Optimized pricing tiers
- **Azure PostgreSQL**: Managed PostgreSQL with Hyperscale deployment; IP firewall, Service/Private Endpoints, Azure AD auth; tiered backups with 7-35 day retention; SLA 99.99%
- **Cosmos DB**: Fully managed NoSQL with <10ms latency; globally distributed; multiple APIs (SQL, Mongo, Gremlin, Table, Cassandra); 5 consistency levels (Strong, Bounded Staleness, Session, Consistent Prefix, Eventual); partition-based scaling; RU/s pricing model; up to 99.999% SLA with write replication
- **Azure Redis**: Managed in-memory distributed cache; fully compatible with OSS Redis and Enterprise Redis; IP firewall, Service/Private Endpoints, TLS; 5 tiers (Basic with no SLA, Standard up to 99.9%, Premium up to 99.95%, Enterprise up to 99.99%, Enterprise Flash up to 99.99%); pricing based on tier and instance memory; reservation options available
- **Azure Storage**: Object store for files, documents, videos; 5 types (Blobs, Files, Queues, Tables, Disks); 6 redundancy options (LRS, ZRS, GRS, GZRS, RA-GRS, RA-GZRS); 3 access tiers (Hot, Cool, Archive) with lifecycle rules; Blob security via IP firewall, Service/Private Endpoints, SAS, Access Keys; soft delete with configurable retention; container access levels (Private, Blob, Container); RBAC-based access and SAS tokens with expiry and IP restrictions; VNet restrictions and private endpoints for networking; region failover support; Front Door and Azure CDN integration for edge caching

### Messaging
- **Event Grid**: Event-based pub/sub architecture; no queues, no ordering; strong Azure service integration; built-in HA with 99.99% SLA; max event size 1 MB; up to 10M events/sec, 5000 events/sec/topic; subsecond latency at 99th percentile; pricing based on operations (first 100K free/month)
- **Storage Queue**: Simple queue within Azure Storage Account; no special pricing; same availability as blob storage; 64 KB max message size; 20K messages/sec/account, 2K messages/sec/queue; client libraries available for multiple languages
- **Event Hubs**: Big data streaming and event ingestion service (managed Kafka); millions of events/sec; components include event producers, partitions (single ordered streams, max 32 per deployment, no HA per partition), and consumer groups; AMQP for receiving; SLA 99.95% (Basic/Standard) and 99.99% (Dedicated); throughput measured in Throughput Units (TU = 1 MB/s or 1000 events/s ingress; 2 MB/s or 4096 events/s egress), prepurchased and billed hourly; pricing based on tier, ingress, and TU
- **Service Bus**: Fully managed, durable message queueing service; supports point-to-point (Queue) and pub/sub (Topic); compatible with AMQP and JMS 2.0 (Premium only); advanced features include message sessions (FIFO), dead-letter queues, scheduled delivery, transactions, and duplicate detection; SLA 99.9% with geo-disaster recovery option; security via IP firewall, Service Endpoints, and Private Endpoints; three tiers (Basic 256 KB queues only, Standard 256 KB with topics/transactions/sessions, Premium 1 MB with Geo-DR/resource isolation/AZ support); pricing based on tier and operations

### Identity Management
- **Microsoft Entra ID** (formerly Azure Active Directory): Central identity and access management cloud service; secure, robust, intelligent; manages access to thousands of apps including the Azure Portal
- **Tenant** (Directory): Specific instance of Entra ID containing accounts and groups; exists beside subscriptions (not part of the subscription hierarchy); a new tenant is automatically created for new subscriptions; a single tenant can be assigned to multiple subscriptions

## Content Guidelines When Adding/Editing

1. **Be Concise**: Keep notes focused and exam-relevant
2. **Include Specifics**: Add concrete numbers (SLA percentages, cost savings, time ranges)
3. **Use Bullet Points**: Notes are structured for quick reference
4. **Highlight Key Decisions**: Document factors that influence architecture choices (cost, availability, region selection)
5. **Link Related Concepts**: Cross-reference when topics connect (e.g., VM cost relates to disk types)

## Git Workflow

This is a personal study repository tracked in Git. Standard Git workflow applies:
- Commit logical changes with descriptive messages
- Track progress through commits as study material evolves
