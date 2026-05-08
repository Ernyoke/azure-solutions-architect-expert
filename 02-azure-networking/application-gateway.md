# Application Gateway

- It is a web traffic load balancer
- Can function as the external endpoint of a web app
- Works at **layer 7 (Application)** of the OSI model => can see and understand the content of the traffic (HTTP/HTTPS, headers, URL, cookies)
- Can be **public** (internet-facing) or **private** (internal)
- **SLA: 99.95%** (v2 SKU with 2+ instances)
- It works with:
    - VMs
    - VM Scale Sets
    - App Services / Function Apps
    - Kubernetes via AKS (requires additional configuration)
    - On-prem servers (any backend reachable by IP/FQDN)
- It is similar to a Load Balancer, but it comes with additional features such as:
    - SSL/TLS termination (and end-to-end TLS re-encryption to backend)
    - Autoscaling (v2 only, configurable min/max instance count)
    - Zone redundancy (v2 only, deployable across Availability Zones)
    - Session affinity (cookie-based)
    - URL based routing (path-based rules)
    - Multi-site hosting (host header-based routing)
    - WebSockets and HTTP/2 support
    - Custom error pages
    - Header and URL rewrite
    - HTTP to HTTPS redirection
    - WAF

## Application Gateway vs Load Balancer

| Feature        | Load Balancer       | Application Gateway              |
| -------------- | ------------------- | -------------------------------- |
| OSI Layer      | 4 (Transport)       | 7 (Application)                  |
| Protocols      | TCP, UDP            | HTTP, HTTPS, HTTP/2, WebSocket   |
| Routing        | 5-tuple hash        | URL path, host header            |
| SSL termination| No                  | Yes                              |
| WAF            | No                  | Yes (WAF_v2 SKU)                 |
| Use case       | Internal / non-HTTP | Web apps / APIs                  |

## WAF

- Web Application Firewall, used to protect web apps against common attacks such as XSS, SQL injection, etc.
- Protection is based on rules from the **OWASP Core Rule Set** (CRS)
- This ruleset updates continuously
- WAF can work in **Detection** or **Prevention** mode: detection mode can detect attacks and notify us, but it won't block the traffic; prevention mode blocks the traffic as well
- Many organizations have their own WAF deployment based on 3rd party products (Palo Alto, Fortinet, Imperva, etc.). In these cases there is no need for WAF in the Application Gateway => WAF not required, we can use Standard_V2

## Application Gateway SKUs

- **Standard_V2**: includes all the features mentioned, excluding WAF
- **WAF_v2**: includes everything, including WAF (almost double the price)
- v1 SKUs (Standard, WAF) are **deprecated** — use v2 for new deployments

## Pricing

- Billed per hour based on:
    - **Gateway hours** (fixed cost per instance per hour)
    - **Capacity Units (CU)**: consumed based on compute, persistent connections, and throughput
- WAF_v2 costs roughly **2x** Standard_V2

## Networking

- Application Gateway is placed in its **own dedicated subnet** (often in its own VNet)
    - Subnet can only contain other Application Gateways of the same tier
    - Recommended subnet size: **/24** to allow autoscaling
- Requires a **public IP** (Standard SKU, Static) for internet-facing deployments
- We must make sure that the backend resources are:
    - Accessible from the AG subnet
    - Not accessible from anywhere else (lock down with NSGs / Private Endpoints)

## Configuring Application Gateways

- 5 main configurations:
    - **Frontend IP configurations**: the public (or private) IP exposed by the gateway
    - **Listeners**: receivers that receive requests using specific protocols, ports, host names, and SSL certificates
        - Basic listener: single site
        - Multi-site listener: routes based on host header
    - **Backend pools**: VMs, VM Scale Sets, App Services, IP/FQDN connected to the Application Gateway
    - **HTTP settings**: settings for how the gateway connects to the backend (protocol, port, timeout, cookie affinity, probes)
    - **Rules**: connect listeners to backend pools
        - Basic rule: forwards all traffic from listener to a backend pool
        - Path-based rule: routes based on URL path

## Health Probes

- Default probe: HTTP GET to `/` on backend, expects status `200`–`399`
- **Custom probes** can be configured: path, host, interval, timeout, unhealthy threshold, expected status codes
- Backends marked unhealthy receive no traffic

## Application Gateways and AKS

- There is no builtin integration with Application Gateway and AKS
- AKS has its own kind-of gateway (ingress)
- *Application Gateway Ingress Controller (AGIC)* connects Application Gateway with an AKS cluster - **not recommended** to be used
- *Application Gateway for Containers* is the evolution of the Application Gateway Ingress Controller (recommended replacement)

## Application Gateways and Function Apps

- Function Apps are basically App Services
- They can be protected by Application Gateway the same way App Services are
- Function Apps can be configured in the backend pool for an Application Gateway

## Application Gateway Affinity

- Makes sure users will always be directed to the same instance (VM, App Service)
- Implemented via cookie-based affinity (`ApplicationGatewayAffinity` cookie)
- The affinity feature should be avoided when possible
- Usually required for stateful apps
- Usually a sign of bad design
- We should always try to design stateless apps

## Application Gateway and Cookies

- Problem:
    - The App Service has its own DNS name (builtin, provided by Azure)
    - Usually we want to use our own custom domain for the Application Gateway when building an app on App Service + Application Gateway
    - When we use a custom DNS name with App Service and our app is using cookies, the cookies will be provided for the builtin DNS => the browser will drop the cookies
- Solution:
    - Set custom domain for the App Service to be the same as the one set for the Application Gateway (configure it on the Custom Domains page of the App Service portal)