# ACR - Azure Container Registry

- Managed private registry for Docker and OCI container images and artifacts (Helm charts, OCI artifacts)
- Regional service, images are pulled by AKS, App Service, ACI, Container Apps, Functions

## Service tiers

| Tier | Included storage | Key capabilities |
| --- | --- | --- |
| Basic | 10 GB | Dev/test, lowest throughput |
| Standard | 100 GB | Production workloads, higher throughput |
| Premium | 500 GB | Geo-replication, private link, CMK, content trust, tokens, zone redundancy |

- Premium-only features worth remembering:
    - **Geo-replication** - single registry name replicated to multiple regions, pulls served locally
    - **Private endpoints / private link** - no public exposure
    - **Customer-managed keys (CMK)** for encryption at rest
    - **Content trust** - image signing and verification
    - **Repository-scoped tokens** - fine-grained access to individual repositories
    - Zone redundancy

## Authentication and authorization

- Entra ID based, RBAC roles:
    - `AcrPull` - pull images (use for AKS, App Service, ACI managed identities)
    - `AcrPush` - push and pull
    - `AcrDelete`, `AcrImageSigner`
    - `Owner`/`Contributor` include registry management
- **Managed identity** is the recommended way for services to pull images
- Service principal for CI/CD pipelines
- Admin user (single account, username/password) exists but should stay disabled
- Repository-scoped tokens for partner/limited access (Premium)

## ACR Tasks

- Build and patch container images in Azure, no local Docker needed
- Task types:
    - **Quick task** - `az acr build`, on-demand build in the cloud
    - **Trigger on source code commit** (GitHub/Azure Repos)
    - **Trigger on base image update** - automatic rebuild when the base image is patched
    - **Scheduled** and **multi-step** tasks (build, test, push)

## Image management

- Tags and manifests, multi-arch images
- Retention policy for untagged manifests
- Soft delete for recovering deleted images
- Image import from other registries without a local pull/push

## Security

- Defender for Containers scans images for vulnerabilities on push and continuously
- Disable anonymous pull unless a public registry is intentional
- Use private endpoints + firewall rules with the "trusted services" bypass for AKS
- Enable diagnostic logging of pull/push events

## Design notes

- Use one registry per environment or per landing zone, geo-replicate for multi-region AKS
- Registry name must be globally unique (`<name>.azurecr.io`)
- Pull latency and egress cost drive the geo-replication decision
