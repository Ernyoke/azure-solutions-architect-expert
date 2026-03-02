# Load Balancer

- Is an Azure service the distributes load and checks health of the underlying VMs
- When a VM is not healthy, no traffic is directed to it
- Load balancers can work with VMs or with scale sets
- Load balancers can be public or private
- It operates at layer 4 (Transport) of the OSI model:
    - The load balancer knows about the IP, port, protocol (TCP, UDP) and TLS. It does not know anything about the content of the traffic

## Load Balancer Distribution Algorithm

- It is based on 5 tuple hash:
    - Source IP
    - Source port
    - Destination IP
    - Destination port
    - Protocol type
- Same tuples are used by NSGs
- According to the 5 tuples the LB decides how to distribute traffic
- Azure load balancer types:
    - Basic:
        - Offers no redundancy
        - Open by default
        - Supports up to 300 instances
        - No SLA
        - It is free
    - Standard
        - It is redundant
        - Secured by default
        - Supports up to 1000 instances
        - 99.99% SLA
        - It is not free

## Configuring a Load Balancer

- An LB requires 4 main configurations:
    - Frontend IP configuration: 
        - Public IP exposed by the load balancer
    - Backend pools:
        - The VMs that are connected to the load balancer
    - Health probes:
        - Probes that are used to check the health of a machine
        - Non-healthy VMs will not receive traffic
    - Load balancing rules
        - Rules that specify how the frotend IP will be connected to the backend pool

## Health Probes

-  A non-healthy VM will be marked as `Down`and will not be routed to
- Health checks run in intervals (usually a few seconds)
- Probes can use TCP, HTTP, HTTPS (available for Standard only)
- Configurable unhealthy threshold: how many times can a check fail before the VM is marked as Down (default: 2)
- Probes run on the VM's host => no network traffic from the outside of the host is required
- Probes originate always from the same IP address: `168.63.129.16`
- Traffoc from health checker is allowed by default on an NSG

## When to use Load Balancer

- Great for internal resources
- It is not recommended to be used for external resources:
    - Especially for Web Apps/APIs
    - Can't handle HTTP
    - Does not route based on path
    - Offer no protection
For web apps Application Gateway is the recommended service to be used
