# Azure Front Door

- Azure Front Door is a global Layer 7 application delivery network for HTTP and HTTPS applications
- It combines global load balancing, content delivery network (CDN), dynamic site acceleration, TLS termination and application security
- Unlike Azure Traffic Manager, Front Door is a reverse proxy: client connections terminate at Front Door edge locations
- Features:
    - Anycast routes users to a nearby Front Door edge location
    - Static content caching and compression
    - Global load balancing and rapid failover across origins and regions
    - Cookie-based session affinity for applications that require a user to remain on the same origin
    - Web Application Firewall (WAF), DDoS protection and bot protection
    - Managed certificates for custom domains
    - URL redirection, URL rewriting and request or response header modification
- Azure Front Door Standard and Premium use this resource hierarchy:
    - Profile: top-level resource containing Front Door configuration
    - Endpoint: provides the Front Door hostname to which routes are attached
    - Domain: the default endpoint domain or a custom domain
    - Route: maps domains and URL path patterns to an origin group
    - Origin group: contains one or more origins and their health-probe and load-balancing settings
    - Origin: the application endpoint that serves content
    - Rule set: an ordered collection of rules that can modify request processing

## Tiers

- Standard:
    - Static and dynamic content acceleration
    - Global HTTP(S) load balancing, caching, compression and TLS termination
    - Custom domains and managed certificates
    - WAF with custom rules
- Premium:
    - Includes all Standard features
    - Adds Private Link connectivity to supported origins
    - Adds WAF managed rule sets, including bot protection and Microsoft Threat Intelligence integration
- Azure Front Door (classic) uses older frontend, backend pool and routing-rule terminology and is scheduled for retirement; use Standard or Premium for new deployments

## Routing

- A route maps a domain and matching URL path patterns to an origin group
- Request flow:
    - The client request reaches a nearby Front Door edge location
    - Front Door matches the request to a profile, endpoint, domain and route
    - WAF policy and rule-set logic are evaluated when configured
    - Front Door returns a cached response when an eligible cache entry exists
    - Otherwise, Front Door selects a healthy origin and forwards the request
- Within an origin group, Front Door selects an origin in this order:
    - Health: excludes origins that health probes identify as unhealthy
    - Priority: considers origins with the lowest configured priority number first
    - Latency: considers origins within the configured latency-sensitivity range
    - Weight: distributes traffic among the remaining origins according to their weights
- Session affinity is a separate cookie-based feature, not an origin-selection method

## Origins and Origin Groups

- An origin is an application endpoint identified by a publicly resolvable DNS name or IP address
- Origins can be Azure services or custom endpoints, including App Service, Storage static websites, API Management, Application Gateway and services outside Azure
- Premium can connect privately to supported Azure origins by using Private Link
- Origin settings include:
    - Host name and origin host header
    - HTTP and HTTPS ports
    - Priority from 1 to 5; a lower number has higher priority
    - Weight from 1 to 1000; the default is 500
    - Enabled or disabled state
- Every origin belongs to an origin group
- Origin groups define health probes, load-balancing settings and session affinity
- A route connects an endpoint and one or more domains to an origin group

## Health Probes

- Front Door sends synthetic HTTP or HTTPS requests to determine origin health
- Probe settings include the protocol, path, request method and interval
- An origin is considered healthy when the configured number of recent probe samples succeed
- If an origin becomes unhealthy, Front Door directs new requests to other healthy origins in the group
- Health probes should use a lightweight endpoint that verifies critical application dependencies

## Load-Balancing Settings

- Sample size defines how many recent health-probe results Front Door evaluates
- Successful samples required defines how many samples must succeed for an origin to be healthy
- Latency sensitivity specifies the acceptable latency difference between candidate origins
- A latency sensitivity of 0 ms sends traffic to the lowest-latency origin; a larger value allows weighted distribution among origins within that latency range

## Routes

- Routes map domains and URL path patterns, such as `/api/*`, to an origin group
- A route controls accepted protocols, HTTP-to-HTTPS redirection, forwarding protocol and origin path
- Caching and compression can be enabled per route
- A rule set can be associated with a route for conditional request processing

## Traffic Acceleration

- Front Door accepts traffic at a nearby edge location and carries it across Microsoft's global network toward the origin
- This split-TCP architecture and persistent origin connections improve performance for globally distributed users without application code changes
- Static content can be served directly from edge caches, while dynamic content benefits from route and connection optimization

## Rule Sets

- The rules engine customizes request handling at the edge by evaluating ordered conditions and actions
- Conditions can inspect values such as:
    - Device type and HTTP version
    - Cookies, query strings and POST arguments
    - Client IP address
    - Request file name, extension, header, method, path, protocol or URL
- Operators include equal, contains, begins with, ends with, less than, greater than and regular expression; many support negation and case transformation
- Actions include:
    - Override cache behavior and cache expiration
    - Include or exclude query-string parameters from the cache key
    - Append, overwrite or delete request and response headers
    - Redirect requests with HTTP status 301, 302, 307 or 308
    - Rewrite the request path
    - Override the origin group