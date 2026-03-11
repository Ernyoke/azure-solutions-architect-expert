# AGEMTS.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is a documentation repository containing study notes for the **Azure Solutions Architect Expert (AZ-305)** certification exam. The content is organized as a structured learning resource covering Azure services, concepts, and best practices.

## Repository Structure

The repository follows a numbered directory structure representing different topics:

- `00-basic-concepts/` - Foundational Azure concepts (regions, resource groups, cost, SLA, storage)
- `01-azure-compute/` - Azure compute services (VMs, VM Scale Sets, App Service, Azure Functions, AKS, ARM templates)
- `02-azure-networking/` - Azure networking services (Virtual Networks, Load Balancer, Application Gateway)
- `03-azure-data-services/` - Azure data services (Azure SQL, Databases on VMs)

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
