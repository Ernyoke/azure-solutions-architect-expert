# Azure Firewall

- Azure Firewall is a managed, stateful, cloud-native firewall for controlling north-south and east-west network traffic
- It provides built-in high availability, availability-zone support and automatic scaling
- Deploy it centrally in a hub VNet or a secured Virtual WAN hub and route spoke traffic through it
- It complements NSGs, WAF and DDoS Protection; these controls operate at different scopes and layers

## Architecture

- A VNet deployment requires a dedicated subnet named `AzureFirewallSubnet`; a `/26` or larger subnet is recommended for scaling
- Do not place other resources in `AzureFirewallSubnet`
- The firewall has private IP addresses for routed internal traffic and normally one or more Standard public IP addresses for internet traffic
- Use a hub-spoke topology to share one firewall across peered VNets and connected on-premises networks
- Azure Firewall Manager can deploy and centrally manage policies across hub VNets and secured Virtual WAN hubs
- Deploy across availability zones for datacenter-level resilience; zone selection must be made during deployment

## SKU Selection

| Feature | Basic | Standard | Premium |
| --- | --- | --- | --- |
| Stateful network, application and NAT rules | Yes | Yes | Yes |
| Built-in HA and availability zones | Yes | Yes | Yes |
| Maximum scale throughput | 250 Mbps | 30 Gbps | 100 Gbps |
| Application-rule FQDN filtering | Yes | Yes | Yes |
| Network-rule FQDN filtering | No | Yes | Yes |
| DNS proxy and custom DNS | No | Yes | Yes |
| Threat intelligence | Alert only | Alert and deny | Alert and deny |
| Web categories | No | FQDN-based | Full URL with TLS inspection |
| Forced tunneling | No | Yes | Yes |
| TLS inspection and URL filtering | No | No | Yes |
| IDPS | No | No | Yes |

- Choose **Basic** for small environments needing essential filtering at the lowest cost
- Choose **Standard** for most enterprise filtering, DNS proxy, threat-intelligence blocking, web categories and forced tunneling
- Choose **Premium** for sensitive or regulated workloads requiring TLS inspection, IDPS or filtering by complete URL path
- Azure Firewall has a fixed hourly deployment charge plus a data-processing charge

## Firewall Policy

- Firewall Policy is the recommended resource for defining reusable Azure Firewall rules and security settings
- A policy contains **rule collection groups**, which contain **rule collections**, which contain individual rules
- Rule collection groups and rule collections have numeric priorities; lower numbers are processed first
- A rule collection action is `Allow` or `Deny`; NAT rule collections use `DNAT`
- Policies can be associated with multiple firewalls
- A child policy inherits from a base policy:
    - Base-policy rules are evaluated before child-policy rules
    - A child policy cannot override an explicit deny in the base policy
    - Use the base policy for organization-wide controls and child policies for workload-specific rules
- Policy Analytics identifies frequently used, unused and overly broad rules to support rule cleanup

## Rule Types

### DNAT Rules

- Destination NAT publishes a private resource through the firewall public IP
- A DNAT rule translates the destination public IP and port to a private IP and port
- Match conditions use source addresses, destination public IP, destination port and TCP/UDP protocol
- Preserve symmetric routing so the response returns through the firewall
- Azure Firewall does not provide inbound TLS termination or WAF protection; use Application Gateway or Front Door when HTTP inspection is required

### Network Rules

- Layer 3/4 rules match source, destination, port and protocol
- Use them for TCP, UDP, ICMP and other non-HTTP/S traffic
- Destinations can use IP addresses, service tags and, on Standard/Premium, FQDNs
- Network-rule FQDN filtering depends on DNS proxy so the firewall and client use consistent DNS results

### Application Rules

- Layer 7 outbound rules allow or deny HTTP, HTTPS and supported SQL traffic by FQDN
- Rules can use explicit FQDNs, wildcard FQDNs, FQDN tags and web categories where supported
- Standard filters HTTPS by the TLS Server Name Indication (SNI), not the full encrypted URL
- Premium can inspect TLS and filter the complete URL path

## Rule Processing

1. Threat-intelligence filtering is applied according to its configured mode
2. DNAT rule collections are processed
3. Network rule collections are processed
4. Application rule collections are processed

- Processing stops when traffic matches a terminating rule
- Network rules are evaluated before application rules regardless of their collection priorities
- If no rule allows traffic, Azure Firewall denies it by default
- An allow rule is not enough when routing, DNS or the destination service's own firewall blocks the connection

## SNAT and Outbound Access

- Source NAT translates outbound private addresses to an Azure Firewall public IP, producing predictable addresses for partner allowlists
- Associate multiple public IP addresses or a public IP prefix when more SNAT ports are required
- By default, Azure Firewall does not SNAT traffic to configured private address ranges
- Avoid SNAT port exhaustion by distributing connections across public IPs, reusing connections and monitoring port utilization
- Use NAT Gateway instead when the only requirement is scalable outbound connectivity with stable public IPs and no filtering

## Routing

- Associate a route table with each protected workload subnet
- A typical route sends `0.0.0.0/0` to the firewall private IP using next-hop type **Virtual appliance**
- Add routes for spoke-to-spoke or hybrid traffic when that traffic must also be inspected
- VNet peering is non-transitive; the firewall provides the routed transit path between spokes
- Ensure return traffic follows the same firewall path to avoid asymmetric routing
- Do not add a normal default route to `AzureFirewallSubnet`; forced tunneling uses its dedicated deployment design and management path

## Forced Tunneling

- Forced tunneling sends internet-bound traffic from Azure Firewall to an on-premises firewall or another NVA instead of directly to the internet
- Standard and Premium support forced tunneling; Basic does not
- Forced Tunnel mode separates management traffic from the tenant data path and can deploy the data path without a public IP
- Use forced tunneling when policy requires centralized on-premises internet egress or an existing security stack
- It adds latency and dependency on hybrid connectivity, so provide redundant routes and capacity

## DNS Proxy

- Standard and Premium can act as a DNS proxy, receiving client queries and forwarding them to Azure DNS or configured custom DNS servers
- Configure workload DNS to use the firewall private IP when network rules filter by FQDN
- DNS proxy reduces mismatches between the IP address resolved by the client and the address observed by the firewall
- DNS proxy is not an authoritative DNS service; use Azure DNS/Private DNS for zones and DNS Private Resolver for hybrid forwarding

## Threat Protection

### Threat Intelligence

- Uses Microsoft threat-intelligence feeds to identify traffic to or from known malicious IP addresses and domains
- Modes are Off, Alert only, and Alert and deny where supported
- Basic supports alerting only; Standard and Premium can block matching traffic
- Threat-intelligence filtering has high precedence and can be allowlisted for approved exceptions

### TLS Inspection

- Premium decrypts outbound or east-west TLS traffic, inspects it, then creates a new TLS connection to the destination
- Configure an intermediate CA certificate and ensure clients trust its root chain
- Store and manage the certificate through Azure Key Vault as required by the deployment
- Exclude certificate-pinned or incompatible applications from TLS inspection
- Azure Firewall Premium does not terminate inbound TLS as a reverse proxy; use Application Gateway for that scenario

### IDPS

- Premium IDPS uses managed signatures to detect network exploits and malicious activity
- Modes include Off, Alert, and Alert and deny
- Tune signature overrides and private IP ranges to reduce false positives and classify traffic direction correctly
- Test in Alert mode before enabling blocking for production traffic

## Availability and Scale

- High availability is built in; no load balancer is required in front of Azure Firewall
- Availability-zone deployment protects against a zone failure at no additional firewall deployment charge, though normal processing charges still apply
- The service scales automatically, but scaling takes time; avoid sudden untested traffic bursts and monitor throughput
- For multi-region architectures, deploy a firewall in each region rather than routing all traffic through one region
- Use identical Firewall Policies or inherited policies to keep regional controls consistent

## Monitoring

- Send diagnostic logs and metrics to Log Analytics, Storage or Event Hubs through diagnostic settings
- Important logs include application rules, network rules, NAT rules, DNS proxy, threat intelligence, IDPS and TLS inspection
- Prefer resource-specific Log Analytics tables for structured queries
- Monitor denied traffic, rule hits, throughput, latency, SNAT port utilization and firewall health
- Azure Firewall Workbook provides traffic, threat and rule visualizations
- Use Policy Analytics and flow trace to investigate rule behavior and optimize policy
- Integrate logs with Microsoft Sentinel or another SIEM for security detection and investigation

## Service Comparison

| Requirement | Recommended service |
| --- | --- |
| Subnet/NIC Layer 3/4 micro-segmentation | NSG |
| Central network and FQDN filtering | Azure Firewall |
| Stable outbound IP without filtering | NAT Gateway |
| HTTP exploit protection | WAF on Application Gateway or Front Door |
| Volumetric Layer 3/4 attack protection | Azure DDoS Protection |
| Vendor-specific appliance or unsupported feature | Network Virtual Appliance |

## Exam Design Decisions

- Central filtering across spokes -> **Azure Firewall in the hub + UDRs**
- Small environment, up to 250 Mbps -> **Basic**
- Threat-intelligence blocking, DNS proxy or forced tunneling -> **Standard or Premium**
- TLS inspection, IDPS or full-URL filtering -> **Premium**
- Publish a non-HTTP private endpoint through a public IP -> **DNAT rule**
- Allow outbound HTTPS by domain -> **application rule**
- Filter non-HTTP traffic by IP/port -> **network rule**
- Filter non-HTTP traffic by FQDN -> **Standard/Premium network rule + DNS proxy**
- Central policy across many firewalls -> **Firewall Policy + Azure Firewall Manager**
- Global HTTP WAF -> **Front Door**, not Azure Firewall alone