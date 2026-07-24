# ACI - Azure Container Instances

- The fastest and simplest way to run a container in Azure
- Serverless: no VM to manage, no orchestrator to configure
- Billed per second based on requested vCPU and memory
- Starts in seconds (no OS boot)
- Supports both Linux and Windows containers

## Container groups

- A container group is the top-level resource, similar to a Kubernetes pod
- Containers in a group:
    - Are scheduled on the same host
    - Share the lifecycle, network (IP + ports) and storage volumes
- Multi-container groups are supported on **Linux only**
- Typical size limits: up to 4 vCPU and 16 GB memory per group (varies per region)

## Features

- Public IP with an FQDN, or deployment **into a VNet** with a private IP only
- Persistent storage by mounting an Azure Files share (`emptyDir`, secret and gitRepo volumes also supported)
- Restart policies: `Always`, `OnFailure`, `Never`
- Liveness and readiness probes
- Environment variables and secure environment variables
- Managed identity support for accessing Key Vault, Storage, etc.
- Pulls images from ACR, Docker Hub or any registry

## Limitations

- No built-in autoscaling and no load balancing (put Application Gateway in front if needed)
- No rolling updates or deployment slots
- Not meant for complex, long-running multi-service applications

## When to use

- Simple, single-container applications
- Short-lived tasks and batch/automation jobs (`Never`/`OnFailure` restart policy)
- Build agents and CI jobs
- Burst capacity for AKS through **virtual nodes** (Virtual Kubelet)

## ACI vs Container Apps vs AKS

| | ACI | Container Apps | AKS |
| --- | --- | --- | --- |
| Orchestration | None | Managed (built on AKS) | Full Kubernetes |
| Scaling | Manual | KEDA event-driven, scale to zero | HPA + cluster autoscaler |
| Control | Lowest | Medium | Highest |
| Best for | Single tasks/jobs | Microservices, event-driven apps | Complex platforms, k8s APIs needed |
