# App Service Security Best Practices

## Network Access

- Don't expose the App Service directly to the internet — front it with **Application Gateway** (regional L7 + WAF) or **Azure Front Door** (global L7 + WAF + CDN)
- Use **Access Restrictions** (IP allow/deny rules and service tags) to limit inbound traffic, e.g. only allow traffic from the Application Gateway/Front Door
- When using Front Door, restrict access so the app only accepts traffic with the correct `X-Azure-FDID` header (Front Door ID)
- **Private Endpoint**: give the app a private IP inside a VNet for inbound traffic, removing public exposure (supports on-prem access)
- **VNet Integration** (regional): lets the app make **outbound** calls into a VNet to reach private resources (databases, storage, other apps)
- Use **Service Endpoints** or **Private Endpoints** on backend PaaS services (SQL, Storage, Key Vault) and restrict them to the app's subnet
- **App Service Environment (ASE)**: fully isolated, single-tenant deployment into a VNet for high-scale, security-sensitive workloads
- Enforce **HTTPS Only** and a minimum **TLS version** (1.2+); HTTP is redirected to HTTPS

## Identity & Access (Authentication & Authorization)

- Use **App Service Authentication ("Easy Auth")** — built-in auth/authz with no code, supports **Microsoft Entra ID**, plus social/OIDC providers
- Use **Microsoft Entra ID** as the identity provider and enforce **MFA** and **Conditional Access**
- Use **Managed Identity** (system- or user-assigned) for the app to access other Azure resources (SQL, Storage, Key Vault) without storing credentials
- Apply **RBAC** with least-privilege roles for who can manage the App Service / plan
- Authenticate app-to-app and app-to-API calls with Entra ID tokens instead of API keys where possible

## Secrets & Configuration

- Store secrets, connection strings, and certificates in **Azure Key Vault**, not in app settings or code
- Use **Key Vault references** in App Settings so the app pulls secrets at runtime via its Managed Identity
- Mark app settings as **slot-specific (sticky)** where needed so secrets don't move with slot swaps
- App settings and connection strings are **encrypted at rest** and decrypted only at startup

## Data Protection (Encryption)

- **Encryption in transit**: enforce HTTPS/TLS; TLS can be terminated at Front Door/App Gateway and re-encrypted to the app
- Use **App Service Managed Certificates** (free) or bring your own / Key Vault certificates for custom domains
- **Encryption at rest** for app content and configuration is provided by the platform

## Threat Protection & Monitoring

- Enable **Microsoft Defender for Cloud** (Defender for App Service) for threat detection and security recommendations
- Use a **Web Application Firewall (WAF)** on Application Gateway or Front Door to protect against OWASP Top 10 (XSS, SQL injection)
- Enable **diagnostic logging** and send logs/metrics to **Azure Monitor / Log Analytics** and **Application Insights**
- Follow **Secure Score** recommendations to improve posture
- Keep the runtime stack and dependencies patched; the platform handles OS/infrastructure patching
