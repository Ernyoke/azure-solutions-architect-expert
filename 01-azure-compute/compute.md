# Azure Compute

- It is a set of cloud services for hosting and running applications
- Allows us to upload our code and then run it
- Offers various levels of control and flexibility
- Main compute services:
    - VM - Virtual Machines (IaaS)
    - VMSS - Virtual Machine Scale Sets
    - App Services (PaaS)
    - AKS - Azure Kubernetes Service
    - ACI - Azure Container Instances
    - Azure Container Apps
    - Azure Functions (serverless)
    - Azure Batch (parallel / HPC workloads)
    - Azure Virtual Desktop (virtualized desktops and apps)

## The responsibility spectrum

```
More control                                             Less management
IaaS  ------------------------------------------------------------>  Serverless
VM  ->  VMSS  ->  AKS  ->  Container Apps  ->  App Service  ->  ACI  ->  Functions
```

- The further right, the less infrastructure to manage but the fewer customization options
- Prefer the most managed option that still satisfies the requirements (PaaS-first)

## How to chose the right compute type?

- **Lift and shift, legacy app, OS-level access or specific software required** -> Virtual Machines
- **Same as above but needs elastic scale of identical instances** -> VM Scale Sets
- **Web app or API, no OS control needed** -> App Service
- **Event-driven, short-running, spiky or unpredictable load, pay-per-execution** -> Azure Functions
- **Containers, needs full Kubernetes API, service mesh, custom controllers** -> AKS
- **Containers, microservices, want scale-to-zero without managing Kubernetes** -> Container Apps
- **A single container or a one-off task** -> Container Instances
- **Thousands of parallel tasks, HPC, rendering, simulations** -> Azure Batch
- **Remote desktops or published apps for users** -> Azure Virtual Desktop

## Decision factors

- Level of control needed over the OS and runtime
- Migration effort allowed (rehost vs refactor vs rearchitect)
- Scaling pattern: steady, scheduled, bursty or event-driven
- Cost model: reserved/always-on vs consumption
- Availability requirements (availability zones, multi-region, SLA)
- Networking and isolation requirements (VNet integration, private endpoints)

