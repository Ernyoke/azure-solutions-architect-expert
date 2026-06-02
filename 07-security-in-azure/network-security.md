# Network Security

## Networking Security Best Practices

- VNets that contain private resources only should not be exposed to the internet
- Always use NSGs to restrict access to subnets
- Use Service Endpoints/Private Endpoints to restrict access to resources
- Use the Hub-and-Spoke security model

## Network Security Groups (NSG)

- Layer 4 (TCP/UDP) stateful packet filter that allows/denies traffic based on the **5-tuple** (source, source port, destination, destination port, protocol)
- Can be associated to a **subnet** and/or a **NIC** (both are evaluated; most specific applies)
- Rules are evaluated by **priority** (100–4096, lower number = higher priority); first match wins
- Default rules allow VNet-to-VNet and Load Balancer inbound, deny all other inbound, and allow all outbound to the internet
- **Service Tags** represent groups of IPs for Azure services (e.g., `Internet`, `VirtualNetwork`, `AzureLoadBalancer`, `Storage`, `Sql`) so you don't manage IP ranges manually
- NSGs are **free**

## Application Security Groups (ASG)

- Logical grouping of VMs/NICs by **workload/role** (e.g., `web`, `app`, `db`) instead of explicit IP addresses
- Used as source/destination in NSG rules, so rules follow the workload as it scales
- Simplifies rule management in micro-segmented networks

## Azure Firewall

- Fully managed, stateful, cloud-native **network firewall** as a service (PaaS) with built-in HA and unrestricted scalability
- Operates at **Layer 3–7**; centralizes filtering for traffic across VNets, subscriptions, and on-prem
- Rule types: **NAT rules** (inbound DNAT), **Network rules** (L3/L4 by IP/port/protocol), **Application rules** (L7 by FQDN)
- Features: **FQDN filtering**, **FQDN tags**, **threat intelligence**-based filtering, **forced tunneling**, DNS proxy
- Uses a **static public IP** for outbound (SNAT) — useful for allow-listing on partner systems
- **Azure Firewall Manager**: central policy and route management across multiple firewalls/hub VNets
- Tiers: **Standard** vs **Premium** (TLS inspection, IDPS, URL filtering, web categories)
- Typically deployed in the **hub** VNet of a hub-and-spoke topology

## Web Application Firewall (WAF)

- Protects web apps from common exploits (**OWASP Top 10**: SQL injection, XSS, etc.) using managed rule sets
- Available on **Application Gateway** (regional), **Azure Front Door** (global), and **Azure CDN**
- Modes: **Detection** (log only) and **Prevention** (block)

## DDoS Protection

- **Basic**: always-on, free, automatically protects the Azure platform infrastructure
- **DDoS Network Protection** (paid): tuned protection for resources in a VNet, attack analytics/telemetry, alerting, **cost protection** (credits for scale-out during attacks), and access to the DDoS rapid response team
- **DDoS IP Protection**: pay per protected public IP, for smaller deployments
- Protects against **L3/L4 volumetric** attacks; combine with **WAF** for L7 protection

## Secure Access to PaaS

- **Service Endpoints**: route subnet traffic to a managed service over the Azure backbone; resource keeps a public IP but can restrict to selected subnets; **free**; no on-prem support
- **Private Endpoint / Private Link**: assigns a **private IP** inside the VNet to the managed service; traffic stays private and works from **on-prem**; paid; requires **private DNS** configuration
- Prefer **Private Endpoints** for the most secure/private connectivity and on-prem access

## Secure Remote / Admin Access

- **Azure Bastion**: managed, browser-based RDP/SSH over TLS; no public IP or open RDP/SSH ports on VMs
- **Just-in-Time (JIT) VM Access** (Defender for Cloud): open management ports on demand, time-limited, from approved IPs
- **Jump Box**: bastion host VM in a dedicated subnet for admin hops
- **VPN**: encrypted tunnel into the VNet

## Hybrid Connectivity

- **Site-to-Site VPN**: IPsec/IKE tunnel over the internet between on-prem and an Azure VNet Gateway
- **Point-to-Site VPN**: individual client devices connect to the VNet
- **ExpressRoute**: private, dedicated connection (via a provider) that **does not traverse the public internet** — higher reliability, lower latency, more security/bandwidth
- **ExpressRoute + VPN failover**: VPN as a backup path for ExpressRoute
- **VNet Peering**: private connectivity between VNets (same or cross-region); non-transitive

## Network Segmentation & Topology

- **Hub-and-Spoke**: shared services (firewall, gateway, Bastion, DNS) in a central **hub**; workloads isolated in **spoke** VNets peered to the hub
- **Azure Virtual WAN**: managed global transit network connecting VNets, branches, and users at scale
- **User-Defined Routes (UDR) / Route Tables**: override default routing to force traffic through an NVA or Azure Firewall (forced tunneling)
- **NVA (Network Virtual Appliance)**: third-party firewall/router from the marketplace for custom routing/filtering

## Edge & Global Delivery

- **Azure Front Door**: global L7 load balancer + CDN with WAF, SSL offload, and path-based routing
- **Application Gateway**: regional L7 load balancer with WAF, SSL termination, and URL routing
- **Azure CDN**: cache content at edge POPs close to users

## Encryption in Transit

- Use **TLS/HTTPS** for application traffic; terminate/inspect at Application Gateway or Front Door
- Azure backbone traffic between datacenters is encrypted by the platform
- **VPN (IPsec)** and **ExpressRoute** secure hybrid links

## Monitoring & Diagnostics

- **Network Watcher**: connection troubleshoot, **IP flow verify**, next hop, topology, packet capture
- **NSG Flow Logs / Traffic Analytics**: visualize and analyze traffic patterns and detect anomalies
- Send network logs/metrics to **Log Analytics** and configure **alerts**