# Azure Arc

- Extends the Azure management and governance control plane to resources outside Azure
- Manages on-premises, edge and other-cloud resources as Azure Resource Manager resources
- Resources receive an Azure resource ID and can be organized with subscriptions, resource groups and tags
- Azure Arc does **not** migrate a workload to Azure or turn a connected machine into an Azure VM

## Resource Types

| Arc-enabled resource | Purpose |
| --- | --- |
| Servers | Manage Windows and Linux physical servers or VMs outside Azure |
| Kubernetes | Govern and operate Kubernetes clusters running outside Azure |
| VMware vSphere | Inventory and perform supported lifecycle operations on VMware resources |
| System Center Virtual Machine Manager | Manage supported SCVMM clouds and virtual machines through Azure |
| SQL Server | Inventory, assess, secure and govern SQL Server instances outside Azure |
| Data services | Run supported Azure data services on customer-managed Kubernetes infrastructure |

## Arc-Enabled Servers

- Install the **Azure Connected Machine agent** on each Windows or Linux machine
- The agent creates a server resource in Azure and communicates outbound over HTTPS
- No inbound connection from Azure is required for normal agent communication
- Each connected machine receives a system-assigned managed identity
- The Azure region selected during onboarding stores the resource metadata; it does not move the machine or its data
- Supported management capabilities include:
    - Azure Policy and machine configuration
    - Azure Update Manager
    - Azure Monitor Agent and Log Analytics
    - Microsoft Defender for Cloud
    - VM extensions for post-deployment configuration and monitoring
    - Change tracking and inventory through supported Azure services

## Arc-Enabled Kubernetes

- Connects an existing CNCF-certified Kubernetes cluster to Azure by deploying Arc agents into the cluster
- Supports clusters on premises, at the edge and in other public clouds
- Provides Azure Resource Manager inventory, RBAC, tags and policy assignment
- **GitOps with Flux** synchronizes cluster configuration from a Git repository
- Azure Policy can audit or enforce Kubernetes configuration through policy extensions
- Integrates with Azure Monitor and Defender for Containers
- Azure Arc manages the connected cluster but does not provide or operate its underlying control plane, nodes or network

## Resource Bridge

- Azure Arc resource bridge is a customer-managed appliance that connects Azure to supported private-cloud platforms
- Used by Arc-enabled VMware vSphere and SCVMM for inventory and supported VM lifecycle operations
- Enables self-service and Azure-based governance without moving the virtual machines to Azure
- The appliance and its platform connectivity remain the customer's operational responsibility

## Governance and Security

- Apply Azure Policy to Arc resources at management group, subscription or resource-group scope
- Use Azure RBAC for least-privilege management of the Azure resource representation
- Tags support inventory, ownership and cost-allocation conventions across hybrid estates
- Use Defender for Cloud to assess security posture and enable workload protection plans
- Use Azure Monitor to centralize logs, metrics and alerts where the required agents are installed
- Restrict onboarding permissions and use service principals or managed identities for automated onboarding
- Use Azure Arc Private Link Scope when management traffic must use private connectivity where supported
- Removing the Arc resource or agent stops Azure management but does not delete the underlying server or cluster

## Pricing

- Core Azure Arc control-plane capabilities are generally available without an additional Arc fee
- Connected Azure services such as Defender for Cloud, Azure Monitor, Update Manager and data services can incur charges
- Network egress, Log Analytics ingestion and the underlying infrastructure remain separately billed

## When to Use

- Apply consistent Azure Policy and RBAC across Azure, on-premises and multicloud resources
- Centralize hybrid server inventory, monitoring, patching and security posture
- Manage Kubernetes configuration consistently with GitOps
- Expose supported private-cloud VM operations through Azure Resource Manager
- Meet data residency or latency requirements by keeping workloads outside Azure while using Azure management services

## When Not to Use

- Use **Azure Migrate** when the goal is to assess and move servers into Azure
- Use **Azure Lighthouse** when a service provider needs delegated management across Azure tenants
- Use **Azure Stack Hub or Azure Local** when Azure-consistent infrastructure must run on premises
- Use native Azure VM management when the machines already run as Azure VMs

## Exam Design Decisions

- Govern on-premises and multicloud servers from Azure -> **Arc-enabled servers**
- Apply Azure Policy to an external Kubernetes cluster -> **Arc-enabled Kubernetes**
- Deploy Kubernetes configuration continuously from Git -> **Azure Arc GitOps with Flux**
- Manage VMware or SCVMM virtual machines through Azure -> **Azure Arc resource bridge**
- Protect non-Azure servers with Defender for Cloud -> **Onboard with Azure Arc, then enable the Defender plan**
- Move an on-premises server to Azure -> **Azure Migrate, not Azure Arc**
- Delegate an Azure subscription to another tenant -> **Azure Lighthouse, not Azure Arc**