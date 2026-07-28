# Azure API Management

- Fully managed **API gateway** that publishes, secures, transforms, maintains and monitors APIs
- Acts as a **facade** in front of backend APIs (App Service, Functions, AKS, Container Apps, Logic Apps, VMs, on-prem or 3rd party endpoints)
- Backends are decoupled from consumers: the backend can change without breaking clients
- Main use cases:
    - Expose internal APIs to external partners or customers
    - Consolidate multiple APIs behind a single hostname
    - Add authentication, rate limiting, caching and logging without changing backend code
    - Version and revise APIs safely
    - Monetize APIs (products, subscriptions, quotas)

## Components

- **API gateway**: the runtime component that receives calls, applies policies and forwards requests to the backend
    - Verifies API keys, JWT tokens and certificates
    - Enforces quotas and rate limits
    - Caches responses
    - Transforms requests/responses
    - Emits logs, metrics and traces
- **Management plane** (Azure portal / ARM / CLI): define APIs, schemas, policies, products, users
- **Developer portal**: auto-generated, customizable website where consumers discover APIs, read docs, try calls ("try it" console) and get subscription keys
- **Self-hosted gateway**: containerized gateway deployed on-prem, in other clouds or in Kubernetes; managed from Azure but runs traffic locally (data residency, low latency to backend)

## Concepts

- **API**: a set of operations (endpoints) mapped to a backend; can be imported from OpenAPI/Swagger, WSDL, WADL, gRPC, OData, GraphQL, Azure resources
- **Operation**: a single method + URL template (e.g. `GET /orders/{id}`)
- **Product**: a bundle of one or more APIs published to consumers
    - Can be **open** (no subscription needed) or **protected** (requires subscription)
    - Has its own terms of use, quotas and policies
- **Subscription**: grants access to a product/API and provides a pair of **subscription keys** (primary + secondary for rotation)
- **Groups**: control which users see which products (built-in: Administrators, Developers, Guests; plus Entra ID groups)
- **Versions and revisions**:
    - **Version**: a breaking change exposed side-by-side (path, query string or header based versioning)
    - **Revision**: a non-breaking change that can be tested and then promoted to current
- **Named values**: reusable key/value pairs (can be secrets or Key Vault references) used in policies
- **Backends**: reusable backend service definitions, including credentials and circuit breaker settings

## Policies

- XML documents that define the gateway behavior; the core power of API Management
- Applied at scopes, from broad to narrow: **global (all APIs) → product → API → operation**
- `<base />` controls where the parent scope policy is executed
- Sections: `inbound`, `backend`, `outbound`, `on-error`
- Common policies:
    - **Security**: `validate-jwt`, `validate-client-certificate`, `check-header`, `ip-filter`, `authentication-managed-identity`
    - **Traffic control**: `rate-limit`, `rate-limit-by-key`, `quota`, `quota-by-key`
    - **Caching**: `cache-lookup` / `cache-store` (internal cache or external Redis)
    - **Transformation**: `set-header`, `set-body`, `rewrite-uri`, `xml-to-json`, `json-to-xml`, `find-and-replace`
    - **Routing**: `set-backend-service`, `forward-request`, `retry`, `send-request`
    - **Cross-cutting**: `cors`, `mock-response`, `trace`, `emit-metric`
- Policy expressions (`@(...)`, `@{...}`) allow C# snippets for dynamic behavior

## Security

- Client-to-gateway authentication options:
    - Subscription keys (`Ocp-Apim-Subscription-Key` header or `subscription-key` query parameter)
    - OAuth 2.0 / OpenID Connect with Microsoft Entra ID (validated by `validate-jwt`)
    - Client certificates (mutual TLS)
    - IP allow/deny lists
- Gateway-to-backend authentication options:
    - **Managed identity** (preferred, no secrets)
    - Client certificates
    - Keys/headers stored as named values backed by **Key Vault**
- Combine with **Application Gateway / Front Door + WAF** in front of API Management for OWASP protection
- Use **Private Endpoints** or internal VNet mode to keep the gateway private
- **Defender for APIs** (Defender for Cloud) provides threat detection for APIs published in API Management

## Networking

- **Public**: gateway has a public IP and is reachable from the internet (default)
- **VNet integration modes** (Developer and Premium tiers, classic VNet injection):
    - **External**: gateway and developer portal are reachable from the internet, but can reach VNet/on-prem backends
    - **Internal**: gateway is only reachable from inside the VNet or connected networks; typically fronted by Application Gateway for external exposure
- **Private Endpoint**: inbound private connectivity for supported tiers
- **Outbound VNet integration** is available in the newer Standard v2 / Premium v2 tiers without full injection
- Custom domains with TLS certificates (Key Vault integration recommended for auto-rotation)

## Tiers

| Tier | Purpose | SLA | Key notes |
| ---- | ------- | --- | --------- |
| **Consumption** | Serverless, pay-per-call | 99.95% | No developer portal customization, no VNet, no built-in cache; auto-scales to zero |
| **Developer** | Dev/test only | none | Full features including VNet, but **not for production** |
| **Basic** | Entry production | 99.95% | No VNet, no multi-region |
| **Standard** | Mid-level production | 99.95% | No VNet injection, no multi-region |
| **Premium** | Enterprise | 99.99% (multi-region / zones) | **Multi-region deployment**, VNet injection, availability zones, self-hosted gateway, multiple custom domains |
| **v2 tiers** (Basic v2, Standard v2, Premium v2) | Faster provisioning | 99.95%+ | Simplified networking (private endpoints, outbound VNet integration) |

- Scale is measured in **units**; Premium supports units spread across multiple regions
- **Multi-region** (Premium): a primary region hosts the management plane, secondary regions host gateways; traffic is routed to the closest gateway; combine with **Front Door** or **Traffic Manager** for failover

## Monitoring

- **Azure Monitor** metrics (capacity, requests, duration) and diagnostic logs
- **Application Insights** integration for request telemetry and dependency tracking (sampling recommended for high volume)
- **API analytics** in the portal: usage by API, product, subscription, geography
- Built-in **request tracing** for debugging policy execution

## Exam Hints

- Need a single, secured entry point for many APIs with throttling and keys → **API Management**
- Need to expose an internal API to partners without exposing the backend → **API Management (internal mode) + Application Gateway**
- Need multi-region API gateway with 99.99% SLA → **Premium tier + Front Door**
- Need APIs running on-prem but managed centrally in Azure → **self-hosted gateway**
- Need cheapest option for spiky, low-volume APIs → **Consumption tier**
- API Management is **not** a load balancer replacement: use Front Door / Application Gateway / Load Balancer for traffic distribution and WAF
