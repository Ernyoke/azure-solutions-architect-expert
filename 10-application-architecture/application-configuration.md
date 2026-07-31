# Application Configuration Management

- Application configuration should be stored outside application binaries and container images so it can change without rebuilding the application
- Separate **non-secret configuration** from **secrets**:
    - Azure App Configuration stores settings, feature flags and references to secrets
    - Azure Key Vault stores secrets, certificates and cryptographic keys
- Applications should use a **managed identity** to access both services and avoid stored credentials

## Azure App Configuration

- A managed, centralized store for application settings shared across services, regions and deployment environments
- Values are key-value pairs; keys can use prefixes such as `app1:production:` to create a hierarchy
- **Labels** provide variants of the same key, commonly by environment, version, region or tenant
- Supports point-in-time snapshots and revision history for auditing and rollback
- Does not replace Key Vault because configuration values are not designed to hold secrets

## Feature Management

- **Feature flags** enable or disable functionality without redeploying code
- Filters can target a percentage of users, a time window or selected users/groups
- Feature flags support:
    - Canary releases
    - Ring-based deployments
    - Emergency feature disablement
    - A/B testing
- Removing a feature flag from the store does not remove the corresponding dormant code; old flags and code paths need lifecycle management

## Dynamic Configuration

- Applications can refresh selected configuration values without restarting
- Use a sentinel key when multiple related values must be refreshed as one logical change
- Cache configuration locally so temporary service unavailability does not immediately break the application
- Choose a refresh interval that balances propagation speed against request volume and cost
- Configuration changes should be validated and deployed through a controlled pipeline rather than edited directly in production

## Key Vault Integration

- App Configuration can store a **Key Vault reference**, while the secret value remains in Key Vault
- The application resolves the reference using its own managed identity and requires permissions on both resources
- This keeps settings discoverable in one configuration model without exposing secret values
- Use secret versioning and rotation; avoid pinning a version when the application must automatically consume rotated secrets
- Grant least-privilege data-plane roles with Azure RBAC

## Networking and Resiliency

- Use Private Endpoints and disable public network access for isolated workloads
- Private endpoints require correct private DNS resolution from every consuming network
- For multi-region applications, consider a configuration store per region to avoid a regional dependency and automate synchronization through the deployment pipeline
- App Configuration geo-replication can provide replicas for regional resilience; clients should have retry and failover behavior

## Alternatives

| Requirement | Recommended option |
| --- | --- |
| Shared settings and feature flags | Azure App Configuration |
| Secrets, keys and certificates | Azure Key Vault |
| App Service-specific settings | App Service application settings |
| Kubernetes-native settings | ConfigMaps for non-secrets; Key Vault provider for secrets |
| Container Apps settings | Environment variables and secrets; App Configuration for shared dynamic settings |
| Infrastructure configuration | Bicep, ARM templates or Terraform |

## Exam Design Decisions

- Central settings used by multiple applications -> **Azure App Configuration**
- Passwords, connection credentials or certificates -> **Azure Key Vault**
- Release functionality without deploying again -> **feature flags**
- Change settings without restarting -> **dynamic refresh**
- Access without credentials in code -> **managed identity**
- Private-only access -> **Private Endpoint + private DNS**
- App Configuration and Key Vault are complementary, not competing services