# AGENTS.md

## Repository Purpose

This is a documentation repository containing study notes for the **Azure Solutions Architect Expert (AZ-305)** certification exam. The content is organized as a structured learning resource covering Azure services, concepts, and best practices.

## Repository Structure

The repository follows a numbered directory structure representing different topics:

- `00-basic-concepts/` - Foundational Azure concepts (regions, resource groups, cost, SLA, storage)
- `01-azure-compute/` - Azure compute services (VMs, App Service, Azure Functions, AKS)
- `02-azure-networking/` - Azure networking services (Virtual Networks, Load Balancer, Application Gateway)
- `03-azure-data-services/` - Azure data services (Azure SQL, MySQL, PostgreSQL, Cosmos DB, Redis, Storage)
- `04-messaging-in-azure/` - Azure messaging services (Event Grid, Event Hubs, Service Bus, Storage Queue)
- `05-identity-management/` - Identity and access management (Microsoft Entra ID)
- `06-monitoring-in-azure/` - Monitoring services (Resource Monitoring, Azure Monitor, Resource Tags)
- `07-security-in-azure/` - Security services (network, VM, App Service, database, Defender, Key Vault)
- `08-disaster-recovery/` - Disaster recovery and global resiliency services (Azure Backup, Azure Site Recovery, Azure Front Door)

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

- **Foundations**: Regions, availability, SLAs, costs, resource groups and storage accounts
- **Compute**: VMs, App Service, Azure Functions and AKS
- **Networking**: VNets, load balancing and Application Gateway
- **Data**: Relational databases, Cosmos DB, Redis and Azure Storage
- **Messaging**: Event Grid, Event Hubs, Service Bus and Storage Queue
- **Identity**: Microsoft Entra ID, RBAC, managed identities and hybrid identity
- **Monitoring**: Azure Monitor, metrics, logs, alerts and resource tags
- **Security**: Network, workload, application, database, Defender and Key Vault controls
- **Resiliency**: Recovery objectives, Azure Backup, Site Recovery and Front Door

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
