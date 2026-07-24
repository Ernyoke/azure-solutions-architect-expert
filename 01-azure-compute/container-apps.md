# Azure Container Apps

- Serverless container platform for microservices and event-driven applications
- Built on top of AKS, KEDA, Dapr and Envoy, but the Kubernetes API is **not** exposed
- No cluster, node pool or control plane to manage

## Environment

- The environment is the secure boundary around a set of container apps
- Apps in the same environment:
    - Share the same VNet
    - Share the same Log Analytics workspace
    - Can call each other over the internal network by app name
- Can be deployed into an existing VNet (internal or external ingress)

## Scaling

- Powered by **KEDA** (Kubernetes Event-Driven Autoscaling)
- Scale rules based on:
    - HTTP concurrent requests
    - TCP connections
    - CPU / memory
    - Event sources (Service Bus queue length, Event Hubs, Storage Queue, Kafka, custom)
- **Scale to zero** is supported (min replicas = 0) - pay nothing when idle
- Min/max replica counts per app

## Revisions and traffic

- A revision is an immutable snapshot of an app version
- Single revision mode or multiple revision mode
- Traffic splitting across revisions by percentage:
    - Blue/green deployments
    - A/B testing
    - Canary rollouts

## Dapr integration

- Distributed Application Runtime, enabled per app as a sidecar
- Building blocks: service invocation, state management, pub/sub, bindings, secrets, observability
- Removes the need to code service discovery and retry logic

## Workload profiles

- **Consumption** - serverless, scale to zero, pay per vCPU-second and GiB-second
- **Dedicated** - reserved compute (including memory-optimized and GPU), predictable cost, VNet isolation

## Container Apps Jobs

- Run to completion instead of running continuously
- Trigger types:
    - Manual (on demand)
    - Schedule (cron expression)
    - Event-driven (KEDA scalers, for example queue messages)

## Other features

- Built-in HTTPS ingress with free managed TLS certificates and custom domains
- Built-in authentication (Easy Auth) with Entra ID and social providers
- Managed identity, Key Vault secret references, private endpoints
- Health probes (liveness, readiness, startup)
- Logs and metrics through Log Analytics / Azure Monitor

## When to use

- Containerized microservices without wanting to operate Kubernetes
- Event-driven or bursty workloads that should scale to zero
- Background processing jobs
- Choose AKS instead when you need direct Kubernetes API access, custom controllers, service mesh or Windows containers
