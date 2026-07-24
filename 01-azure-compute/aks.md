# AKS - Azure Kubernetes Service

- Managed Kubernetes service on Azure
- Allows us to deploy containers and manage them using Kubernetes on Azure
- With AKS we pay only for the instances (VM) used by AKS. We don't have access to these instances, AKS is a managed service

## Kubernetes

- The most popular container management platform
- De-facto standard for container management
- Released by Google in 2014
- Provides all aspects of container management:
    - Routing
    - Scaling
    - High-Availability
    - Automated deployments
    - Configuration management
    - etc.
- The based building block in Kubernetes is the pod
- A pod can run one or more containers
- The pod exposes a private IP address, this is how the kubernetes control-plain communicates with the pd
- Pods are accessible to the public networks via a Kubernetes service
- A service provides not just a public IP, it can provide load-balancing, monitoring, HA and more

## Cluster architecture

- **Control plane** is managed by Azure (API server, scheduler, etcd) - free in the Free tier
- **Node pools** are the customer's VMs and are billed as normal VMs
    - **System node pool** - runs the critical system pods (CoreDNS, metrics server), Linux only, at least 1 node
    - **User node pools** - run the application workloads, can scale to zero
    - Multiple pools allow different VM sizes, GPU nodes, Spot nodes and Windows nodes
- Spot node pools give large discounts for interruptible workloads
- Nodes should be spread across **availability zones** for zone resiliency

## Pricing tiers and SLA

| Tier | Use |
| --- | --- |
| Free | Dev/test, no financially backed SLA (99.5% uptime target) |
| Standard | Production, 99.95% uptime SLA with availability zones, 99.9% without |
| Premium | Adds long-term support (LTS) for Kubernetes versions |

## Scaling

- **HPA (Horizontal Pod Autoscaler)** - scales pod replicas based on CPU/memory or custom metrics
- **Cluster Autoscaler** - adds/removes nodes when pods cannot be scheduled
- **KEDA** - event-driven pod autoscaling (queue length, Event Hubs, etc.), available as an AKS add-on
- **Virtual nodes** - burst into Azure Container Instances without adding VMs
- **Vertical Pod Autoscaler** - adjusts pod CPU/memory requests

## Networking

- Network models:
    - **Azure CNI** - pods get IPs from the VNet subnet (needs a large address space, direct VNet connectivity)
    - **Azure CNI Overlay** - pods use a separate overlay CIDR, saves VNet IP space (recommended for large clusters)
    - **kubenet** (legacy) - pods use a logically separate address space with UDRs
- **Ingress**: managed NGINX (app routing add-on), Application Gateway Ingress Controller (AGIC), or Gateway API
- **Private cluster** - the API server is exposed only through a private endpoint
- **Authorized IP ranges** restrict public API server access
- Egress control through Azure Firewall / UDR, or NAT Gateway for stable outbound IPs
- Network policies (Azure or Calico / Cilium) restrict pod-to-pod traffic

## Identity and security

- Cluster identity: **managed identity** (system or user assigned)
- Workload access to Azure resources: **Microsoft Entra Workload ID** (federated, replaces pod identity)
- **Entra ID integration + Azure RBAC for Kubernetes** to authorize users against the Kubernetes API
- **ACR integration** - attach the registry so nodes pull images with the managed identity (`AcrPull`)
- **Azure Policy add-on** for AKS enforces Gatekeeper/OPA constraints (no privileged pods, allowed registries)
- **Defender for Containers** - image scanning, runtime threat detection
- **Key Vault provider for Secrets Store CSI driver** mounts secrets into pods
- Node OS auto-upgrade channels and cluster upgrade channels keep versions patched

## Storage

- Volumes via CSI drivers:
    - **Azure Disk** - ReadWriteOnce, best performance, single-pod attach
    - **Azure Files** - ReadWriteMany, shared access via SMB/NFS
    - **Azure NetApp Files** / **Blob CSI** for specialized needs
- Storage classes define dynamic provisioning and reclaim policy

## Operations

- **Container Insights** (Azure Monitor) and Managed Prometheus + Managed Grafana for monitoring
- Node image and Kubernetes version upgrades should use surge upgrades and pod disruption budgets
- Use multiple node pools + taints/tolerations to isolate workloads
