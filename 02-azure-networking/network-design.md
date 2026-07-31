# Network Design

- Network architecture must satisfy connectivity, latency, throughput, availability, security, manageability and cost requirements
- Use private connectivity for internal tiers and expose only the application entry point
- Plan non-overlapping IP address spaces before creating VNets or connecting on-premises networks

## Internet Inbound Connectivity

| Requirement | Recommended service |
| --- | --- |
| Direct access to one resource | Standard Public IP, only when necessary |
| Regional Layer 4 TCP/UDP load balancing | Azure Load Balancer |
| Regional Layer 7 HTTP routing and WAF | Application Gateway |
| Global Layer 7 HTTP acceleration, routing and WAF | Azure Front Door |
| DNS-based routing for any public endpoint/protocol | Traffic Manager |
| Publish and govern APIs | API Management |

- Avoid public IP addresses on application VMs; place them behind a load balancer, Application Gateway or Front Door
- Health probes prevent new traffic from reaching unhealthy backends
- Public endpoints should use TLS, DDoS protection where appropriate and least-privilege inbound rules

## Outbound Connectivity

- Production workloads should use an explicit outbound method rather than relying on default outbound access
- **NAT Gateway** provides scalable outbound-only internet connectivity and predictable public source IP addresses for all resources in an attached subnet
- NAT Gateway performs source network address translation (SNAT); it does not accept unsolicited inbound connections
- Attach a public IP prefix when downstream systems need a predictable allowlist and multiple outbound IP addresses
- Each public IP provides 64,512 SNAT ports; NAT Gateway allocates ports dynamically across subnet resources
- Use Azure Firewall instead when outbound traffic requires centralized network/application rules, FQDN filtering, TLS inspection or threat intelligence
- A Standard Load Balancer outbound rule can provide SNAT but NAT Gateway is generally the preferred dedicated outbound option
- User-defined routes can force outbound traffic through Azure Firewall or a network virtual appliance

## DNS Design

### Azure DNS

- Azure DNS hosts public DNS zones on Azure's global authoritative DNS infrastructure
- It does not register a domain; purchase the domain from a registrar and delegate the zone with name-server records
- DNS records map names to endpoints but do not proxy or inspect traffic
- Alias records can point the zone apex to supported Azure resources without a fixed IP address

### Azure Private DNS

- Private DNS zones resolve names only from linked VNets
- **Virtual network links** make a private zone resolvable from one or more VNets
- Auto-registration can create and remove VM records for one linked VNet; other resource types generally require explicit records or service integration
- Private Endpoint creation commonly uses a `privatelink` private DNS zone so the service name resolves to the endpoint's private IP
- Avoid creating incomplete private zones that override public resolution without containing all required records

### Azure DNS Private Resolver

- A managed service for DNS resolution between Azure and on-premises networks without deploying DNS forwarder VMs
- An **inbound endpoint** receives DNS queries from on-premises or connected networks
- An **outbound endpoint** sends queries according to a linked DNS forwarding ruleset
- A forwarding ruleset maps DNS suffixes such as `corp.contoso.com` to target DNS server IP addresses
- Link the ruleset to every VNet that must use its conditional forwarding rules
- For hybrid name resolution:
    - On-premises DNS conditionally forwards Azure private-zone queries to the resolver inbound endpoint
    - Azure workloads forward on-premises namespace queries through the resolver outbound endpoint

## VNet Topologies

### Hub-Spoke

- A hub VNet hosts shared connectivity and security services such as VPN/ExpressRoute gateways, Azure Firewall, Bastion and DNS Private Resolver
- Spoke VNets isolate workloads and peer with the hub
- VNet peering is non-transitive: spoke A cannot reach spoke B through the hub unless routing is provided by Azure Firewall, an NVA or Virtual WAN
- Configure **gateway transit** on the hub and **use remote gateways** on spokes to share a VPN or ExpressRoute gateway
- Use UDRs to send spoke traffic through a central inspection device; verify return routes to prevent asymmetric routing

### Azure Virtual WAN

- A Microsoft-managed transit service for connecting many branches, VNets and remote users through managed virtual hubs
- Supports site-to-site VPN, point-to-site VPN, ExpressRoute and VNet connections
- Standard Virtual WAN supports transitive connectivity through hubs and can integrate Azure Firewall as a secured virtual hub
- Choose Virtual WAN for large global branch networks and simplified managed transit
- Choose a customer-managed hub-spoke design when fine-grained control or unsupported NVAs and routing behavior are required

## Global Routing

| Service | Routing layer | Endpoint requirement | Best use |
| --- | --- | --- | --- |
| Traffic Manager | DNS | Any internet-accessible endpoint | Global routing for HTTP or non-HTTP protocols |
| Front Door | Layer 7 reverse proxy | HTTP/HTTPS origins | Global web acceleration, TLS offload and WAF |
| Application Gateway | Regional Layer 7 proxy | Regional/private backends | Regional web routing and WAF |
| Load Balancer | Regional Layer 4 | Azure IP backends | High-performance TCP/UDP balancing |

- Traffic Manager returns an endpoint DNS record; clients connect directly to that endpoint, so failover depends on DNS TTL and caching
- Front Door terminates client connections at the edge and uses the Microsoft backbone to reach healthy origins
- Traffic Manager routing methods include priority, weighted, performance, geographic, multivalue and subnet
- Front Door can combine latency-based origin selection, caching, health probes and rules for global web applications

## Network Performance

- Place communicating resources in the same region and, when appropriate, the same availability zone to minimize latency
- Use **Accelerated Networking** on supported VMs for lower latency, jitter and CPU overhead
- Use proximity placement groups when very low latency between VM-based components is more important than zone-level fault isolation
- Use CDN or Front Door caching to serve static content close to users
- ExpressRoute provides private, predictable connectivity; **ExpressRoute FastPath** sends traffic directly from the edge to VNet resources and bypasses the gateway data path where supported
- Global VNet peering uses the Microsoft backbone but has data-transfer charges
- Choose gateway and firewall SKUs based on measured throughput, connection count and packet size, not only advertised maximum bandwidth
- Use load balancing and autoscaling so performance does not depend on one instance

## Network Security

| Control | Scope and purpose |
| --- | --- |
| NSG | Distributed Layer 3/4 filtering on subnets and NICs |
| Application Security Group | Logical grouping used in NSG rules |
| Azure Firewall | Central stateful network and application filtering |
| WAF | Protect HTTP applications from web exploits |
| DDoS Network Protection | Enhanced DDoS protection for public IP resources in protected VNets |
| Private Endpoint | Private IP access to a specific PaaS resource |
| Service Endpoint | Restrict a PaaS public endpoint to selected VNet subnets |

- Combine controls at different layers; WAF does not replace Azure Firewall or NSGs
- Prefer Private Endpoints when PaaS resources must have no public exposure or require private access from on-premises
- Use service tags instead of maintaining Azure service IP ranges manually
- Enable flow logs, firewall logs and diagnostic settings for investigation and compliance

## Network Operations

- Network Watcher provides topology, connection troubleshoot, IP flow verify, next hop, packet capture, VPN troubleshoot and connection monitoring
- **IP flow verify** tests whether NSG rules allow or deny a packet
- **Next hop** identifies the route selected for a destination
- **Connection troubleshoot** tests end-to-end reachability and latency
- **Connection Monitor** continuously monitors connectivity between endpoints
- Use effective routes and effective security rules to diagnose unexpected routing or filtering

## Exam Design Decisions

- Stable outbound public IP for an entire subnet -> **NAT Gateway**
- Central outbound FQDN filtering -> **Azure Firewall**
- Public authoritative DNS hosting -> **Azure DNS**
- Private PaaS name resolution -> **Private DNS zone**
- Hybrid DNS without forwarder VMs -> **DNS Private Resolver**
- Many global branches and managed transit -> **Virtual WAN**
- Global non-HTTP routing -> **Traffic Manager**
- Global HTTP acceleration and WAF -> **Front Door**
- Regional private HTTP routing and WAF -> **Application Gateway**
- Diagnose an NSG decision -> **IP flow verify**
- Diagnose an unexpected route -> **Next hop/effective routes**