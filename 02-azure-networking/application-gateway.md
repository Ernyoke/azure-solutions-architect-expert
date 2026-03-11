# Application Gateway

- It is a web traffic load balancer
- Can function as the external endpoint of a web app
- It works with:
    - VMs
    - VM Scale Sets
    - App Services
    - Kubernetes via AKS (requires additional configuration)
- It is similar to a Load Balancer, but it comes with additional features such as:
    - SSL termination
    - Autoscaling
    - Zone redundancy
    - Session affinity
    - URL based routing
    - WebSockets and HTTP/2 support
    - Custom error pages
    - Header and URL rewrite
    - WAF
- In contrast to a Load Balancer, Application Gateway works at layer 7 (Application) of the OSI model => the Application Gateway can see and understand the content of the traffic

## WAF

- Web Application Firewall, used to protect web apps against common attacks such as XSS, SQL injection, etc.
- This is protection is based on rules from the OWASP Cure Rule Set
- This ruleset updates continuously
- WAF can work in Detection or Prevention mode: detection mode can detect attacks and notify us, but it wont block the traffic; prevention mode blocks the traffic as well
- Many organizations have their onw WAF deployment based on 3rd party products (Palo Alto, Fortinet, Imperva, etc.). In this cases there is no need for WAF in the Application Gateway => WAF not required, we can use Standard_V2

## Application Gateway SKUs

- Standard_V2: includes all the features mentioned, excluding WAF
- WAF_v2: includes everything, including WAF_v2 (almost double the price)

## Networking

- Application Gateway is placed in its own subnet (often in its own VNet)
- We must make sure that the backend resources are:
    - Accessible from the AG subnet
    - Not accessible from anywhere else

## Configuring Application Gateways

- 5 main configurations:
    - Backend pools: VMs, VM scale sates, App Service connected to the Application Gateway
    - HTTP settings: settings for incoming requests
    - Frontend IP configurations: the public IP exposed by the gateway
    - Listeners: receivers the receive requests using specific protocols and ports
    - Rules: rules connected listeners ot backend pools

## Application Gateways and AKS

- There is no builtin integration with Application Gateway and AKS
- AKS has its own kind-of gateway
- *Application Gateway Ingress Controller (AGIC)* connects Application Gateway with an AKS cluster - not recommeded to be used
- *Application Gateway for Containers* is the evolution of the Application Gateway Ingress Controller

## Application Gateways and Function Apps

- Function Apps are basically App Services
- They can be protected by Application Gateway the same way App Services are
- Function Apps can be configured in the backend pool for an Application Gateway

## Application Gateway Affinity

- Makes sure users will always be directed to the same instance (VM, App Service)
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