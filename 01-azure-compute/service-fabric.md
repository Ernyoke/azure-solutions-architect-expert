# Azure Service Fabric

- Distributed systems platform for packaging, deploying and managing scalable microservices and containers
- Supports both **stateless** and **stateful** services
- Runs on Windows or Linux in Azure, on premises or in other clouds
- Service Fabric is used internally by Azure services including Azure SQL Database, Cosmos DB, Event Hubs and IoT Hub

## Cluster Architecture

- A cluster is a network-connected set of physical or virtual machines called **nodes**
- A **node type** defines a group of nodes with the same VM size, configuration and role; in Azure it maps to a Virtual Machine Scale Set
- Every cluster has one **primary node type** that runs critical Service Fabric system services
- Add non-primary node types to isolate workloads, use different VM sizes or scale workloads independently
- **Fault domains** separate replicas across physical failure boundaries
- **Upgrade domains** divide nodes into groups that are upgraded sequentially
- Prefer **Service Fabric managed clusters** for simplified deployment, scaling, patching and certificate management

## Application and Service Model

- An application type is a versioned package containing service types, code, configuration and data
- Multiple named application instances can be created from the same application type
- **Stateless service** instances do not keep authoritative local state and can be placed on any suitable node
- **Stateful service** partitions maintain state through a primary replica and secondary replicas
- Stateful writes require a quorum, providing consistency and availability during node failures
- Partition services to distribute state and throughput; choose partition keys that avoid hot partitions

## Programming and Hosting Options

- **Reliable Services** provides APIs for stateless and stateful services with replicated Reliable Collections
- **Reliable Actors** implements the virtual actor pattern for many independent stateful objects
- **Guest executables** package existing applications without requiring Service Fabric APIs
- **Containers** allow services written in any language to run under Service Fabric orchestration
- An application can mix process-based services and containerized services

## Reliability and Scaling

- Service Fabric continuously monitors service and node health, restarts failed instances and moves replicas when required
- Placement constraints and service affinity control where services run
- Scale stateless services by changing the instance count
- Scale stateful services with partitions and replica counts; adding replicas improves availability but does not partition throughput
- Scale the cluster by changing the node count or adding node types
- Capacity metrics and load reports help the Cluster Resource Manager balance services across nodes

## Deployment and Operations

- Rolling application upgrades process one upgrade domain at a time
- Health policies can pause or automatically roll back an unhealthy upgrade
- Use side-by-side application versions for controlled upgrades and rollback
- Secure node-to-node and client-to-cluster traffic with certificates; use Microsoft Entra ID for management access where supported
- Use Azure Monitor, Application Insights and Service Fabric health events for monitoring
- Back up stateful workloads according to their recovery requirements; replication is high availability, not backup

## When to Use

- Existing Service Fabric application or operational expertise
- High-density microservices that mix containers and processes
- Low-latency stateful services that benefit from colocating compute and replicated state
- Requirement to run the same platform in Azure and on premises

## Service Selection

| Requirement | Recommended service |
| --- | --- |
| Existing Service Fabric workload or Reliable Services/Actors | Service Fabric |
| Standard Kubernetes APIs, ecosystem and portability | AKS |
| Serverless containers without cluster management | Azure Container Apps |
| Web application without container orchestration requirements | App Service |

- Service Fabric provides powerful stateful programming models but requires specialized development and cluster operations knowledge
- For new general-purpose container platforms, prefer AKS or Container Apps unless Service Fabric's stateful model or compatibility is a specific requirement
