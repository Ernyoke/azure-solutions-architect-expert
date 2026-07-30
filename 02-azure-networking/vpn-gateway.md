# VPN Gateway

- A managed Azure service that sends **encrypted traffic over the public internet** between a VNet and another network
- Deployed into a dedicated subnet called `GatewaySubnet` (name is mandatory, `/27` or larger recommended)
- Uses IPsec/IKE (IKEv1 and IKEv2) tunnels
- Only **one** VPN gateway per VNet (a VNet can also have one ExpressRoute gateway alongside it)
- Provisioning takes ~30-45 minutes

## Gateway types

| Gateway type | Purpose |
| --- | --- |
| `Vpn` | IPsec tunnels over the internet (site-to-site, point-to-site, VNet-to-VNet) |
| `ExpressRoute` | Gateway for a private ExpressRoute circuit (no encryption by default) |

## Connection types

### Site-to-Site (S2S)

- Connects an **on-prem network** to a VNet
- Requires a VPN device on-prem with a public IPv4 address
- Modeled in Azure with a **Local Network Gateway** (represents the on-prem network: public IP + address prefixes)
- Always-on tunnel, good for hybrid scenarios and as a cheap backup for ExpressRoute

### Point-to-Site (P2S)

- Connects an **individual client machine** to a VNet (remote workers, admins)
- No VPN device required, client software/native VPN client is used
- Protocols:
    - **OpenVPN (SSL/TLS, port 443)** - most firewall friendly, cross-platform
    - **SSTP (SSL, port 443)** - Windows only
    - **IKEv2** - cross-platform, needs UDP ports open
- Authentication options: Azure certificate, RADIUS, **Microsoft Entra ID** (OpenVPN only)

### VNet-to-VNet

- Connects two VNets across regions/subscriptions/tenants through gateways
- Encrypted, but slower and more expensive than **VNet peering**
- Prefer peering unless encryption over the wire or transitive routing via a gateway is required

## SKUs

| SKU | S2S tunnels | P2S connections | Aggregate throughput | Zone-redundant |
| --- | --- | --- | --- | --- |
| Basic | 10 | 128 (SSTP only) | 100 Mbps | No (legacy, no Entra auth, no active-active) |
| VpnGw1 / Az | 30 | 250 | 650 Mbps | `Az` SKUs only |
| VpnGw2 / Az | 30 | 500 | 1 Gbps | `Az` SKUs only |
| VpnGw3 / Az | 30 | 1000 | 1.25 Gbps | `Az` SKUs only |
| VpnGw4 / Az | 100 | 5000 | 5 Gbps | `Az` SKUs only |
| VpnGw5 / Az | 100 | 10000 | 10 Gbps | `Az` SKUs only |

- `AZ` SKUs (`VpnGw1AZ`...`VpnGw5AZ`) are deployed across **availability zones**
- Basic SKU cannot be resized to the newer SKUs (requires recreate), avoid it for production
- Throughput is shared across all tunnels on the gateway

## High availability

- **Active-Standby** (default) - two instances, one active, failover in ~10 s (planned) to ~1.5 min (unplanned)
- **Active-Active** - both instances active, each with its own public IP, on-prem device builds two tunnels
- **Dual-redundancy** - active-active on both the Azure and on-prem side (4 tunnels)
- **Zone-redundant** - use an `AZ` SKU with a zone-redundant public IP
- **ExpressRoute + S2S VPN as failover** - common design for critical hybrid connectivity

## Routing

- **Policy-based (static)** - Basic SKU only, single S2S tunnel, traffic selected by address prefixes
- **Route-based (dynamic)** - default and recommended, required for P2S, VNet-to-VNet, coexistence and multiple tunnels
- **BGP** supported on route-based gateways (not Basic): dynamic route exchange, transit routing, better failover
- Gateway transit: a peered VNet can use the hub's VPN/ExpressRoute gateway (`Use remote gateways` + `Allow gateway transit`)

## Pricing

- Charged per **gateway-hour** by SKU, plus **outbound data transfer**
- P2S is additionally billed per connection-hour above the SKU's included amount
- Basic ~ lowest cost, VpnGw5 the most expensive; the gateway runs 24/7 even with no traffic

## VPN Gateway vs ExpressRoute vs Virtual WAN

|  | VPN Gateway | ExpressRoute | Virtual WAN |
| --- | --- | --- | --- |
| Transport | Public internet (encrypted) | Private circuit via partner | Managed hub, supports both |
| Bandwidth | Up to 10 Gbps | 50 Mbps - 100 Gbps | Scales per hub |
| Latency | Variable | Predictable, low | Depends on connection type |
| SLA | 99.9% (99.95% active-active / zone-redundant) | 99.95% | 99.95% |
| Cost | Low | High | Medium-high |
| Setup time | Minutes-hours | Weeks (provider dependent) | Hours |
| Use case | Small/medium hybrid, branch offices, DR link | Mission-critical, high volume, compliance | Many branches, global transit routing |

## Exam tips

- "Connect on-prem to Azure quickly / cheaply" -> **Site-to-Site VPN**; "predictable low latency, high bandwidth, private" -> **ExpressRoute**
- "Individual remote users need access to VNet resources" -> **Point-to-Site**
- "Encrypt traffic between two VNets" -> VNet-to-VNet VPN; otherwise **peering** (cheaper, lower latency)
- "Many branch offices with global transit" -> **Azure Virtual WAN**
- Entra ID authentication for P2S requires the **OpenVPN** protocol
- Multiple tunnels, BGP or coexistence with ExpressRoute all require **route-based** routing (not Basic SKU)
- The gateway must live in a subnet named exactly `GatewaySubnet`, with no NSG restrictions that break control traffic
