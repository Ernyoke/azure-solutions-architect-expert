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
