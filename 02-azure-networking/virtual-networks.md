# Virtual Networks - VNet

- Virtual networks are fundamental blocks of our private networks in the Azure Cloud
- Virtual networks are places where we can deploy cloud resources to be able to have secure communication between them
- Not all services support VNets, examples of those which do: VMs, App Services, databases, Azure Functions (premium and dedicated plans), etc.
- Resources in a VNet can communicate with each other by default, but they cannot communicate with resources from other VNets (by default, can be modified)

## Pricing

- VNets are free, but there is a limit of 50 VNets per subscription across all regions

## VNet characteristics

- A VNet is scoped to a single region and it cannot span across multiple regions
- A VNet is scoped to a single subscription
- VNets can be connected via Peering
- VNets are segmented using subnets and subnets are protected using NSGs (Network Security Group)

## Security

- The most important thing to take in consideration when designing a network: *how to limit access to the resources in the VNet so that risk is minimized?*

## Addresses of VNets

- Each VNet has its own address rage (IP Range)
- By default this is 65_536 addresses
- The range can be customized
- The address range is expressed using CIDR notation

## CIDR Notation

- Classless Inter-Domain Routing
- A method for representing an IP range
- Composed of an address in the range and a number between 0 and 32
- The number indicates the number of bits that are allocated to the address. The smaller the number - the larger the range
- Example: `109.186.149.240/24` => range is between `109.186.149.000` and `109.186.149.255`

## Subnets

- A subnet is a logical segmnet in the network
- Shares a subset of the VNet's IP range, used as a logical groups of resources in the VNet
- It is a must. Resources must be placed in subnet, they cannot be placed directly ina VNet
- Resources in a subnet can talk to other resouces in other subnets in the same VNets (by default, it can be customized)
- Addressess of subnets:
    - For a subnet we should NEVER use the full range of the VNet
    - The address range is hard to be modified after creation => after a VNet/subnet creation it is hard to add future subnets
    - Subnet pricing
- Subnet pricing:
    - Subnets are free in a VNet
    - There is a limit of 3_000 subnets per VNet

## NSG - Network Security Groups

- NSGs are gatekeepers for subnets: they define who can connect in and out of a subnet
- We can think of an NSG as a mini-firewall
- NSGs should be part of a subnet creation
- NSGs are free
- How does an NSG work?
    - An NSG looks at the 5 tuples:
        - Source: where did the connection come from
        - Source port: the port of the source
        - Destination: where does the connection request go
        - Destination port: to which port does the source want to connect
        - Protocol: TCP, UDP, both
    - Based on the 5 tuples a connection is either allowed or denied
- Security Rules: 
    - A security rule is a security policy applied to the NSG, specifies if the traffic can be allowed/denied based on the 5 tuples
    - Each rules has a number: the lower the number the higher the priority of the rule
    - An NSG is automatically created and attached to every newly created VM's network interface
    - By default the NSG of a VM opens the RDP (on Windows) or SSH (on Linux) port to anyone
    - This rule must be handled first after the VM's creation
    - Default security rules:
        - Azure creates the following default rules in each NSG thet we create:
            - `AllowVNetInBound`
                - Priority: 65000
                - Protocol: Any
                - Source: VirtualNetwork
                - Destination: VirtualNetwork
                - Access: Allow
            - `AllowAzureLoadBalancerInBound`
                - Priority: 65001
                - Protocol: Any
                - Source: AzureLoadBalancer
                - Destination: 0.0.0.0/0
                - Access: Allow
            - `DenyAllInbound`
                - Priority: 65500
                - Protocol: Any
                - Source: 0.0.0.0/0
                - Destination: 0.0.0.0/0
                - Access: Deny
- Service Tags:
    - In addition to setting up security rule per IP, we can also set up rules that use Service Tags
    - These service tags represent well known sources which can be used in the rule, and we don't have to know their IP address
    - Examples:
        - Internet: all the requests coming from the internet
        - VirtualNetwork: requests coming from other Azure's Virtual Networks (this service tag is used by the default rule created for every NSG)
        - AzureLoadBalancer: requests coming from Azure Load Balancer (this service tag is used by the default rule created for every NSG)
        - ApiManagement
        - GatewayManager
        - AzureActiveDirectory
        - AzureActiveDirectoryDomainServices
        - AzureMonitor: requests coming from Azure Monitor, and are used for monitoring the app
        - AzureBackup

## Network Peering

- Sometimes, to increase security, we want to place resources in completly different VNets. For example:
    - Separate systems that we place in different VNets
    - System layers: front-end in one VNet, business layer (data access layer) in a different VNet
    - Sensitive databases placed in different VNets
- Resources in different VNets might not be able to communicate with each other => to solve this we can use network peering
- With network peering:
    - We allow VNets to connect to each other
    - From the user's perspective the communication looks like it is happening in a single VNet
    - We should make sure that the address space of the VNets do not overlap
    - We should use NSGs for protection
- Peering can work across regions
- Network peering is not free: we pay for outbound and inbound traffic ($0.01 per 100 GB)