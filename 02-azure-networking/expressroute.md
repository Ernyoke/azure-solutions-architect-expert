# Azure ExpressRoute

- Extends an on-premises network into Microsoft cloud services through a **private connection** provided by a connectivity partner
- Traffic does not travel over the public internet, giving more predictable latency, reliability and throughput than a site-to-site VPN
- Supports Azure services, Microsoft 365 and Dynamics 365 through separate peering configurations
- ExpressRoute does **not encrypt traffic by default**; use MACsec on ExpressRoute Direct or IPsec over Microsoft peering when encryption is required

## Core components

| Component | Purpose |
| --- | --- |
| ExpressRoute circuit | Logical connection between the connectivity provider and Microsoft edge routers |
| Service provider | Supplies the physical connectivity through a supported location |
| Peering location | Facility where the provider connects to the Microsoft global network; it does not determine the Azure resource region |
| ExpressRoute gateway | Connects an ExpressRoute circuit to one or more Azure VNets |
| BGP | Dynamically exchanges routes between the on-premises network and Microsoft |

- A circuit is identified by a **service key**, which is shared with the provider to provision connectivity
- Azure and the provider must both finish provisioning before the circuit can carry traffic
- Each peering uses two redundant BGP sessions across redundant Microsoft edge routers

## Connectivity models

- **CloudExchange co-location** - connect through a virtual cross-connection at an exchange facility
- **Point-to-point Ethernet** - dedicated Ethernet connection between the on-premises site and Microsoft
- **Any-to-any (IPVPN)** - integrate Azure into an existing provider-managed WAN or MPLS network
- **ExpressRoute Direct** - connect directly to Microsoft routers at 10 or 100 Gbps for very high bandwidth, large-scale circuit management and MACsec support

## Peering types

### Azure private peering

- Connects on-premises networks to VNets and private IP addresses
- Requires an ExpressRoute virtual network gateway unless using Virtual WAN
- Supports bidirectional private route exchange through BGP
- Multiple VNets can link to the same circuit, subject to circuit and gateway limits

### Microsoft peering

- Connects to Microsoft public services such as Microsoft 365, Dynamics 365 and Azure services exposed through public endpoints
- Requires public IP address ranges owned by the customer or connectivity provider
- Uses route filters to select Microsoft 365 service communities
- Microsoft 365 over ExpressRoute requires authorization because internet access is the recommended default

- **Azure public peering is deprecated**; Azure public services use Microsoft peering

## SKUs and bandwidth

| Option | Key characteristics |
| --- | --- |
| Local | Access to Azure regions in or near the selected peering location; no separate outbound data charge |
| Standard | Access to Azure regions within the same geopolitical region; up to 10 linked VNets |
| Premium add-on | Global Azure region access, more route prefixes and higher VNet link limits; required for Global Reach |

- Provider circuits offer bandwidth tiers from **50 Mbps to 10 Gbps**
- ExpressRoute Direct supports **10 Gbps and 100 Gbps** ports
- The selected bandwidth is shared by the peerings on the circuit
- Circuit bandwidth can generally be increased without disruption if the provider supports it; decreasing it requires recreating the circuit

## ExpressRoute gateway

- Must be deployed in a subnet named exactly `GatewaySubnet`
- Gateway SKUs determine throughput and scale; `ErGw1AZ`, `ErGw2AZ` and `ErGw3AZ` provide zone-redundant deployment
- **FastPath** sends eligible traffic directly between the on-premises network and VNet resources, bypassing the gateway data path to improve performance
- Gateway transit lets peered spoke VNets use the gateway in a hub VNet
- A VNet can have both an ExpressRoute gateway and a VPN gateway for coexistence and failover

## Resiliency

- Each circuit includes two redundant connections to Microsoft edge routers; configure both BGP sessions
- End-to-end resiliency requires redundant customer routers and provider links, not only redundant Microsoft routers
- For maximum resiliency, use circuits in **different peering locations** and, where possible, different providers
- **Zone-redundant gateways** protect the Azure gateway layer from availability-zone failure
- A site-to-site VPN can provide backup connectivity, but VPN routes must be designed so ExpressRoute remains preferred during normal operation
- ExpressRoute circuits have a **99.95% availability SLA** when both redundant connections are correctly configured

## Routing and connectivity features

- ExpressRoute uses **BGP**; static routing is not supported
- Azure prefers ExpressRoute over VPN when the same route is learned from both paths
- ExpressRoute connections are not transitive by default: two on-premises sites connected to separate circuits cannot communicate through Microsoft unless **ExpressRoute Global Reach** is configured
- **Global Reach** privately connects on-premises networks through the Microsoft backbone and requires Premium-capable circuits
- **ExpressRoute Global Reach** connects on-premises sites; **global VNet peering** connects VNets
- Forced tunneling and user-defined routes must be planned carefully to avoid asymmetric routing

## Pricing

- Charges include the circuit port, the ExpressRoute gateway and any Premium or Global Reach add-ons
- **Metered Data** plans charge for outbound data; inbound data is included
- **Unlimited Data** plans include inbound and outbound transfer for a higher fixed circuit fee
- **Local** circuits include unlimited data transfer but have a smaller regional reach
- The connectivity provider bills its service separately from Azure

## ExpressRoute vs VPN Gateway

|  | ExpressRoute | VPN Gateway |
| --- | --- | --- |
| Transport | Private provider connection | Public internet |
| Encryption | Not encrypted by default | IPsec/IKE encrypted |
| Performance | Predictable latency, up to 100 Gbps with Direct | Internet-dependent, up to 10 Gbps by SKU |
| Provisioning | Provider coordination; often days or weeks | Azure and VPN device configuration; usually faster |
| Cost | Higher | Lower |
| Best fit | Mission-critical, high-volume or compliance-sensitive hybrid connectivity | Fast, economical hybrid connectivity and backup |

## Exam tips

- "Private, predictable, high-bandwidth connection to Azure" -> **ExpressRoute**
- "Encrypted hybrid connection over the internet" -> **VPN Gateway**
- Connecting VNets to on-premises private networks -> **Azure private peering**
- Accessing Microsoft public services -> **Microsoft peering**; Azure public peering is deprecated
- Connecting two on-premises locations through Microsoft's backbone -> **ExpressRoute Global Reach**
- Improving on-premises-to-VNet data-path performance -> **FastPath**
- Premium expands geographic reach and limits; it does not provide encryption
- A peering location is where the circuit enters Microsoft's network and can differ from the Azure deployment region
- Redundant Microsoft edge routers do not remove the need for redundant customer and provider connectivity