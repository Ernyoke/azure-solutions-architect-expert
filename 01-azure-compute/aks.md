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