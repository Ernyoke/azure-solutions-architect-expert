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
- `06-monitoring-in-azure/` - Monitoring services (Resource Monitoring, Azure Monitor, Resource Tags)
- `07-security-in-azure/` - Security services (Network Security, VM Security)

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
- **Azure SQL Database**: Managed single database with automatic backups, TDE encryption, Always Encrypted, IP firewall, Service/Private Endpoints, Microsoft Entra ID auth; DR via active geo-replication, auto-failover groups, and zone-redundancy (SLA 99.9%-99.995%)
- **Elastic Pool**: Multiple databases sharing compute resources on a single server; cost-effective for low-utilization workloads
- **Managed Instance**: Near 100% on-prem SQL Server compatibility; VNet deployment for lift-and-shift; supports instance-scoped features (SQL Agent, CLR, Linked Servers); vCore only; no serverless tier
- **Azure SQL Pricing**: DTU vs vCore purchase models; vCore service tiers (General Purpose, Business Critical, Hyperscale) and DTU service tiers (Basic, Standard, Premium); Provisioned vs Serverless compute; Hyperscale is Single Database only
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
- **Microsoft Entra ID** (formerly Azure Active Directory): Central cloud identity and access management service; cloud-based (OAuth/OIDC/SAML), distinct from on-prem AD DS (LDAP/Kerberos/GPO)
- **Tenant** (Directory): Specific instance of Entra ID containing accounts and groups; exists beside subscriptions (not part of the subscription hierarchy); a new tenant is automatically created for new subscriptions; a single tenant can be assigned to multiple subscriptions
- **Users and Groups**: Members (internal) vs Guests (external via B2B); dynamic groups require Premium P1
- **Licenses**: Free (500K objects, basic MFA via Security Defaults), Premium P1 (~$6/user/mo: Conditional Access, dynamic groups, SSPR with writeback, Application Proxy, Entra Connect), Premium P2 (~$9/user/mo: risk-based Conditional Access/Identity Protection, PIM, Access Reviews)
- **Security Defaults**: Free-tier baseline (require MFA, block legacy auth); mutually exclusive with Conditional Access
- **Conditional Access** (P1): If-then policies — signals (user/group, app, location, device, risk) → decision (block/grant with conditions) → enforcement; report-only mode for testing
- **Privileged Identity Management (PIM)** (P2): Just-in-time, time-bound activation of privileged roles; eligible vs active assignments with approval/MFA/justification
- **RBAC**: Built-in roles (Owner, Contributor, Reader, User Access Administrator); scope hierarchy Management Group → Subscription → Resource Group → Resource (inherited downward); Azure RBAC roles vs Entra ID roles are separate systems; custom roles supported
- **Managed Identities**: System-assigned (tied to resource lifecycle) vs User-assigned (independent, reusable); avoids credential management
- **App Registrations & Service Principals**: Apps use OAuth 2.0/OIDC and JWT tokens; Service Principal is the in-tenant identity of a registered app; App Service supports Easy Auth
- **Azure AD B2C**: Identity-as-a-service for customer-facing apps; separate tenant; works with social/OIDC providers; being superseded by Microsoft Entra External ID
- **B2B Collaboration**: Invite external guests; guests authenticate against their home tenant, host tenant only grants permissions
- **Hybrid Identity (Entra Connect)**: Syncs on-prem AD to Entra ID; authentication options: Password Hash Sync (PHS, default), Pass-Through Authentication (PTA, no hashes in cloud), Federation (AD FS, most complex); Seamless SSO supplements PHS/PTA
- **Entra Domain Services**: Managed AD DS in Azure (domain join, LDAP, Kerberos, GPO) for lift-and-shift/legacy apps; no domain controllers to manage

### Monitoring
- **Resource Monitoring**: Most Azure resources have a Monitoring section (Insights, Alerts, Metrics, Diagnostic settings, Logs)
- **Alerts**: Composed of conditions (when to trigger), actions (notifications sent to Action Groups), and details (notification content); pricing per metric (~$0.1/metric/month) and per notification type with free monthly tiers
- **Logs**: Generated by nearly every resource (performance, events, errors); require a central repository
- **Log Analytics Workspace**: Centralized location to store, organize, and analyze aggregated logs; queried with Kusto (KQL); region-scoped, ideally co-located with resources to save costs
- **Azure Monitor**: Central location for all monitoring aspects of Azure resources; collects metrics (numerical time-series, ~93 day retention) and logs (timestamped records in Log Analytics, queried with KQL); data sources span application (Application Insights), guest OS, Azure resource, subscription (Activity Log), tenant (Entra ID logs), and custom sources; core building blocks include the metrics database, Log Analytics Workspace, diagnostic settings, Data Collection Rules (DCR), and Activity Log; capabilities beyond individual resources include cross-resource correlation, alerts and Action Groups, Insights, Workbooks/dashboards, Autoscale, and Application Insights APM; Azure Monitor (operational) vs Microsoft Sentinel (cloud-native SIEM/SOAR for security)
- **Resource Tags**: Name-value pairs to organize resources beyond resource groups (e.g., environment, app, owner); can be set at creation or anytime after; useful for resource querying and cost analysis

### Security
- **Networking Security Best Practices**: Keep private-only VNets off the internet; use NSGs to restrict subnets; use Service/Private Endpoints; adopt the hub-and-spoke model
- **Network Security Groups (NSG)**: Layer 4 stateful 5-tuple filter; associated to subnet and/or NIC; priority-based rules (100-4096); Service Tags for Azure service IP groups; free
- **Application Security Groups (ASG)**: Logical grouping of VMs/NICs by workload/role used as source/destination in NSG rules; simplifies micro-segmentation
- **Azure Firewall**: Managed, stateful, cloud-native L3-L7 network firewall (PaaS) with HA; NAT/Network/Application rules; FQDN filtering and tags, threat intelligence, forced tunneling, DNS proxy; static public IP for SNAT; Azure Firewall Manager; Standard vs Premium (TLS inspection, IDPS, URL filtering); deployed in the hub VNet
- **Web Application Firewall (WAF)**: Protects against OWASP Top 10; available on Application Gateway (regional), Azure Front Door (global), and Azure CDN; Detection and Prevention modes
- **DDoS Protection**: Basic (free, platform-level); DDoS Network Protection (paid, VNet-tuned with telemetry, alerting, cost protection); DDoS IP Protection (per-IP); L3/L4 volumetric, combine with WAF for L7
- **Secure Access to PaaS**: Service Endpoints (backbone routing, free, no on-prem) vs Private Endpoint/Private Link (private IP, on-prem support, paid, private DNS); prefer Private Endpoints
- **Secure Remote/Admin Access**: Azure Bastion (browser RDP/SSH over TLS, no public IP/open ports), Just-in-Time (JIT) VM Access, Jump Box, VPN
- **Hybrid Connectivity**: Site-to-Site VPN, Point-to-Site VPN, ExpressRoute (private, off public internet), ExpressRoute + VPN failover, non-transitive VNet Peering
- **Network Segmentation & Topology**: Hub-and-Spoke, Azure Virtual WAN, User-Defined Routes (UDR)/Route Tables, NVAs
- **Edge & Global Delivery**: Azure Front Door (global L7 + CDN + WAF), Application Gateway (regional L7), Azure CDN
- **Encryption in Transit**: TLS/HTTPS terminated at Application Gateway/Front Door; platform-encrypted backbone; VPN (IPsec) and ExpressRoute for hybrid
- **Network Monitoring & Diagnostics**: Network Watcher (IP flow verify, next hop, topology, packet capture), NSG Flow Logs/Traffic Analytics, logs/alerts via Log Analytics
- **VM Security**: Network access (NSGs, Bastion, JIT, jump box, Azure Firewall, no public IP for private VMs); identity and access (Entra ID authentication, least-privilege RBAC, Managed Identities, Key Vault for secrets, MFA/Conditional Access); data protection (SSE at rest by default, Customer-Managed Keys, Azure Disk Encryption, encryption at host, Confidential VMs for data in use); threat protection and patching (Microsoft Defender for Cloud/Defender for Servers, Secure Score, Azure Update Manager, Microsoft Antimalware, boot diagnostics)

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
