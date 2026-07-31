# Automated Application Deployment

- Continuous integration (CI) builds and validates each change; continuous delivery/deployment (CD) promotes a tested artifact through environments
- Build an artifact once and promote the **same immutable artifact** through development, test and production
- Keep application deployment separate from configuration and secret values
- Store deployment definitions in source control and require peer review

## Platform Selection

| Requirement | Recommended option |
| --- | --- |
| Repositories and workflow already hosted in GitHub | GitHub Actions |
| Azure DevOps repos, boards, test plans and enterprise pipelines | Azure Pipelines |
| Simple App Service deployment from a repository | App Service Deployment Center |
| Kubernetes deployment with continuous reconciliation | GitOps with Flux on AKS |
| Container image build close to Azure Container Registry | ACR Tasks |
| Infrastructure deployment | Bicep, ARM templates or Terraform in the pipeline |

- GitHub Actions and Azure Pipelines can both deploy to Azure; choose based on the organization's source control, governance and existing skills
- Deployment Center configures a source-backed App Service pipeline but offers less orchestration than a full multi-stage pipeline

## Pipeline Stages

1. Restore dependencies, compile and run unit tests
2. Run static analysis, dependency and secret scanning
3. Package an immutable application artifact or container image
4. Publish the artifact to a registry or artifact feed
5. Deploy infrastructure through infrastructure as code
6. Deploy the application to a non-production environment
7. Run integration, security and smoke tests
8. Apply approvals and checks for production
9. Deploy progressively, monitor health and either promote or roll back

## Authentication

- Prefer **workload identity federation** through OpenID Connect for GitHub Actions and Azure Pipelines
- Federation exchanges a short-lived external token for an Azure token and avoids stored client secrets
- Use a dedicated managed identity or service principal per environment and grant least privilege at the narrowest practical scope
- Use managed identities for Azure-hosted deployment agents when supported
- Store unavoidable secrets in Key Vault and mask them from logs
- Do not use personal credentials or long-lived publish profiles for production pipelines

## Deployment Strategies

| Strategy | Behavior | Best fit | Main tradeoff |
| --- | --- | --- | --- |
| Recreate | Stop old version, then deploy new | Non-critical workloads | Downtime |
| Rolling | Replace instances in batches | VMSS and AKS | Two versions coexist during deployment |
| Blue-green | Run old and new environments, then switch traffic | Fast rollback and strong isolation | Additional capacity |
| Canary | Send a small percentage of traffic to the new version | Risk reduction with observable traffic | Requires traffic splitting and monitoring |
| Ring-based | Promote through groups of users or regions | Large distributed systems | Slower release process |

- App Service deployment slots support validation, traffic splitting and atomic swap
- Azure Container Apps revisions support weighted traffic splitting
- AKS supports rolling deployments; service mesh, ingress or GitOps tooling can support canary and blue-green patterns
- VM Scale Sets support rolling upgrade policies and health-based replacement
- Feature flags can separate feature release from binary deployment

## App Service Slots

- Deploy to a staging slot, warm it, run tests and then swap it with production
- Settings marked as **deployment slot settings** remain with their slot during a swap; use them for environment-specific values
- A swap can be reversed quickly if the previous production slot remains available
- Slot swap is not a database rollback; schema changes must remain compatible with both application versions

## Approval and Governance

- Use protected branches, required reviews and environment approvals
- Apply automated checks for tests, security findings, policy compliance and deployment health
- Separate duties for sensitive production environments
- Record deployment evidence and approvals for audit requirements
- Use Azure Policy to block noncompliant resources even when a pipeline attempts to create them

## Rollback and Database Changes

- Preserve previous artifacts and deployment definitions
- Trigger rollback from health indicators such as error rate, latency, failed probes or business metrics
- Prefer backward-compatible database changes using the **expand-and-contract** pattern:
    1. Add compatible schema elements
    2. Deploy code that supports old and new schemas
    3. Migrate data
    4. Remove obsolete schema elements in a later release
- Back up data before destructive changes; reverting application code does not restore lost data

## Exam Design Decisions

- No stored cloud credential in CI/CD -> **workload identity federation**
- Test App Service before production and switch quickly -> **deployment slots**
- Small initial audience -> **canary deployment**
- Immediate traffic switch and fast rollback -> **blue-green deployment**
- Kubernetes state continuously matches Git -> **GitOps/Flux**
- Identical environments and repeatability -> **infrastructure as code**
- Release a feature independently of deployment -> **App Configuration feature flag**