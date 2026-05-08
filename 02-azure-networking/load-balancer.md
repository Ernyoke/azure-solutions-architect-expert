# Load Balancer

- Azure service that distributes load and checks the health of the underlying VMs
- When a VM is not healthy, no traffic is directed to it
- Works with VMs or VM Scale Sets
- Can be **public** (internet-facing) or **internal/private** (within a VNet)
- Operates at **layer 4 (Transport)** of the OSI model:
    - The load balancer knows about the IP, port, protocol (TCP, UDP) and TLS pass-through
    - It does not know anything about the content of the traffic (no HTTP awareness, no SSL termination, no URL routing)
- **Zero added latency** (pass-through, not a proxy)
- Free for Basic; Standard is billed per rule and processed data

## Load Balancer Distribution Algorithm

- Based on a **5-tuple hash**:
    - Source IP
    - Source port
    - Destination IP
    - Destination port
    - Protocol type
- Same tuples are used by NSGs
- According to the 5-tuple the LB decides how to distribute traffic
- Distribution modes:
    - **5-tuple** (default): different connections from the same client may land on different backends
    - **Source IP affinity (2-tuple)**: source IP + destination IP → same client always goes to same backend
    - **Source IP + protocol (3-tuple)**: source IP + destination IP + protocol
- Azure load balancer types (SKUs):
    - **Basic**:
        - Offers no redundancy
        - Open by default (no NSG required)
        - Supports up to 300 instances
        - No SLA
        - It is free
        - **Being retired (September 30, 2025)** — use Standard for new deployments
    - **Standard**:
        - It is **zone-redundant** (or zonal)
        - **Secured by default** (closed; explicit NSG rules required)
        - Supports up to 1000 instances
        - **99.99% SLA**
        - It is not free
        - Supports HTTPS health probes, multiple frontends, cross-region LB
    - **Gateway**: high-performance LB for NVAs (Network Virtual Appliances) such as firewalls

## Public vs Internal Load Balancer

- **Public LB**: has a public IP frontend; balances internet traffic to backend VMs
- **Internal LB**: has a private IP frontend; balances traffic inside a VNet (e.g. tier-to-tier in n-tier apps)

## Configuring a Load Balancer

- An LB requires 4 main configurations:
    - **Frontend IP configuration**:
        - Public IP (public LB) or private IP (internal LB) exposed by the load balancer
        - Standard SKU supports multiple frontends
    - **Backend pools**:
        - The VMs or VM Scale Sets connected to the load balancer
    - **Health probes**:
        - Probes used to check the health of a machine
        - Non-healthy VMs will not receive traffic
    - **Load balancing rules**:
        - Rules that specify how the frontend IP is connected to the backend pool (port mapping, protocol, session persistence, idle timeout)
- Additional features:
    - **Inbound NAT rules**: forward a specific port on the frontend to a specific backend VM (e.g. SSH/RDP)
    - **Outbound rules** (Standard only): explicit control over SNAT for outbound connectivity
    - **HA Ports** (Standard internal LB only): single rule load balances all ports/protocols (used with NVAs)

## Health Probes

- A non-healthy VM will be marked as `Down` and will not be routed to
- Health checks run in intervals (usually a few seconds; default 5s)
- Probes can use **TCP**, **HTTP**, **HTTPS** (HTTPS available on Standard only)
- Configurable **unhealthy threshold**: how many consecutive checks can fail before the VM is marked as Down (default: 2)
- Probes run on the VM's host => no network traffic from outside the host is required
- Probes always originate from the same IP address: **`168.63.129.16`** (Azure platform IP)
- Traffic from the health checker is allowed by default on an NSG (via the `AzureLoadBalancer` Service Tag)

## Load Balancer SLA

- **Basic**: no SLA
- **Standard**: 99.99% SLA for the data path, provided 2+ healthy VMs are in the backend pool

## Cross-Region Load Balancer

- Standard SKU feature
- Single global anycast frontend IP
- Distributes traffic across **regional load balancers** in multiple regions
- Provides **geo-proximity routing** and regional failover
- Operates at layer 4 (for HTTP layer-7 global routing use **Front Door** or **Traffic Manager**)

## When to use Load Balancer

- Great for internal resources and non-HTTP workloads (TCP/UDP services, databases, custom protocols)
- It is **not recommended** for external web resources:
    - Especially for Web Apps/APIs
    - Can't handle HTTP-aware routing
    - Does not route based on path or host
    - Offers no protection (no WAF, no SSL termination)
- For web apps, **Application Gateway** (regional, layer 7) or **Front Door** (global, layer 7) are the recommended services
