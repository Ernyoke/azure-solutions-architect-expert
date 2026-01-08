# Virtual Machines

- A virtual server running on a physical server
- Allows creating new servers extremely quickly
- VMs are based on existing resources of the physical server
- Azure VMs are **Unmanaged Services**
- Steps for creating VMs in Azure:
    - Select the location
    - Select the image (OS + Pre-Installed software)
    - Select the size

## Real cost of a VM

- The cost of running a VM includes the following:
    - VM itself
    - Disk
    - IP: not all public IP addresses have a cost
    - Storage: Azure stores the image of the VM in a storage account

## Reducing the cost of a VM

- Most effective techniques are:
    - Auto shutdown:
        - Can be enabled when creating a VM
        - Automatically shuts down the machines when not needed
        - Relevant for test/dev machines
        - Storage and IP (if static) costs still occur
    - Reserved Instances:
        - Azure allows upfront payment with substantial discount for reserved instances
        - Usually offered for 1 or 3 years
        - Great for production machines which run continuously
        - Offers great discounts, up to 62%
        - Can be divided to monthly payments
        - After reserving a machine, the payments cannot be stopped/refunded
    - Spot Instances:
        - Machines that run on unused capacity in Azure
        - Can be evicted at any moment when the capacity is needed
        - Spot instances offers up to 90% discount, but prices fluctuates according to demand
        - Spot instances are great for non-critical, non-continuous tasks, ie. Batch processes, long running calculations
        - Eviction type (can be selected when the VM is created):
            - Capacity only: VM is evicted when Azure needs the capacity for pay as you go workflows. Max price is the pay as you go rate
            - Price or capacity: we can choose a max price and Azure will evict our VM when the cost of the instance is greater the the max price or when Azure needs the capacity for the pay as you go workflow
        - Eviction policy (can be selected at creation):
            - Stop/Deallocate
            - Delete
    - Disk Optimization:
        - When creating the VM we should make sure we select the right disk for the machine
        - For many instance types the default is Premium SSD - the most expensive option
        - For non IO-intensive workflows we can chose Standard SSD (ie. App servers, in-memory cache)
- Other cost saving techniques:
    - Select the right size for the machine (CPU shouldn't rest)
    - Select Linux over Windows when possible
    - Check the price in nearby regions

## Availability of a VM

- VMs that have 2 or more instances deployed across 2 or more AZs in the same region SLA is 99.99%
- VMs that have 2 or more instances deployed in the same Availability Set or in the same Dedicated Host Group the SLA is 99.95%
- For any single instance, the SLA depends on the hard drive:
    - Premium SSD: 99.9%
    - Standard SSD: 99.5%
    - Standard HDD: 95%
- Availability concepts in Azure:
    - Fault Domain
        - Logical group of physical hardware that share a common power (essentially a rack)
    - Update Domain
        - Logical group of physical hardware that can undergo maintenance and be rebooted at the same time
        - Maintenance is done by Azure at its own discretion
        - Host machines in an Update Domain can be shared across more then one Fault Domain
    - Availability Set
        - A collection of Fault Domains and Update Domains our VMs will be spread across
        - Can contain up to 3 Fault Domains and up to 20 Update Domains
        - All domains are in the same AZ
        - Taking advantage of an Availability Set:
            - When we deploy identical VMs we must deploy them into the same AS
            - This will ensure they wont be shut down simultaneously when a single Fault Domain shuts down or an Update Domain reboots
            - If needed, we can deploy a load balancer to route between VMs
            - Availability Sets are free, we pay only for the VMs
    - Availability Zone
        - A physical separate zone withing an Azure region
        - Technically a building containing autonomous data center
        - Each zone functions as a Fault & Update domain
        - Provides protection against a complete zone shutdown => better SLA then AS
        - Taking advantage of Availability Zones:
            - We can deploy identical VMs in separate AZs in the same region
            - This will ensure they wont be down simultaneously when the zone shuts down
            - If needed, we can deploy a load balancer between the VMs
        - Availability Zones are free, we pay only for the VMs

## ARM Templates

- Azure Resource Manager Template
- A JSON file describing the resources to be created
- Used by Azure in (almost) all deployments
- Can be exported, modified, uploaded and deployed
- Can also be created from scratch
- An ARM template is a declarative way of deploying resources:
    - Can deploy multiple resources at once
    - Can be integrated with CI/CD pipelines
    - Can be source controlled