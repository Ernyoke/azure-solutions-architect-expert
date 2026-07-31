# Infrastructure as Code

- Infrastructure as code (IaC) defines infrastructure in version-controlled files instead of relying on manual portal changes
- Benefits include repeatability, review, audit history, automated testing, consistent environments and disaster recovery
- A deployment should be **idempotent**: applying the same desired configuration again should not create unintended changes
- Keep reusable definitions parameterized, but avoid putting secrets in templates, parameter files, outputs or deployment logs

## ARM Templates

- Azure Resource Manager (ARM) templates are native declarative JSON definitions for Azure resources
- ARM resolves dependencies and can deploy independent resources in parallel
- Core elements include parameters, variables, resources, outputs and functions
- Use `dependsOn` only for real dependencies; unnecessary dependencies slow deployment
- Linked or nested templates split large definitions, but Bicep modules are generally easier to author

### Deployment Scopes

- Resource group
- Subscription
- Management group
- Tenant

- The deployment scope controls which resource types can be created and where deployment history is stored
- Use higher-scope deployments for governance resources such as management groups, subscriptions, policy assignments and role assignments

### Deployment Modes

- **Incremental mode** adds or updates resources in the template and leaves other existing resources in the target resource group
- **Complete mode** can delete resources in the target resource group that are absent from the template
- Prefer Deployment Stacks when explicit lifecycle management and deletion behavior are required
- Use the what-if operation before deployment to preview additions, modifications, deletions and no-change resources

## Bicep

- Bicep is a declarative Azure-specific language that compiles to ARM JSON
- It provides concise syntax, type validation, IntelliSense, modules and native support for all ARM resource types and API versions
- Bicep does not require a separate state file because Azure Resource Manager stores the current resource state
- Use modules to create reusable components such as a VNet, application environment or policy baseline
- Pin module versions and publish shared modules to a private Bicep registry in Azure Container Registry or to Template Specs
- Use symbolic names and implicit dependencies when one resource references another

## Template Specs

- A Template Spec stores a versioned ARM template as an Azure resource
- Users deploy the Template Spec without needing access to the original source file or linked artifacts
- Azure RBAC controls who can read or deploy approved templates
- Use Template Specs to distribute governed, versioned infrastructure patterns within an organization
- Updating a Template Spec version does not automatically update resources deployed from an older version

## Deployment Stacks

- A Deployment Stack manages a collection of Azure resources as one lifecycle unit
- When a managed resource is removed from the stack definition, the stack can:
    - Delete the resource
    - Detach it so it remains but is no longer managed by the stack
- A stack can apply deny settings that protect managed resources from unauthorized changes or deletion
- Stacks can operate at resource-group, subscription or management-group scope
- Use Deployment Stacks when a platform team must manage both deployment and cleanup of a complete environment
- A stack is not a CI/CD system; invoke it from Bicep/ARM tooling or a pipeline

## Terraform

- Terraform is a multi-cloud declarative IaC tool using HashiCorp Configuration Language (HCL)
- Providers translate configuration into platform API operations
- Terraform uses a **state file** to map configuration to deployed resources
- Store shared state remotely in a protected Azure Storage account with state locking, versioning, encryption and restricted access
- Typical workflow:
    1. `terraform init` initializes providers and the backend
    2. `terraform plan` previews changes
    3. `terraform apply` executes the plan
- Protect state because it can contain sensitive values
- Detect and reconcile drift; avoid making portal changes to Terraform-managed resources

## Tool Selection

| Requirement | Recommended option |
| --- | --- |
| Azure-native IaC with concise syntax | Bicep |
| Existing ARM JSON ecosystem | ARM templates |
| Versioned, RBAC-controlled Azure template distribution | Template Specs |
| Azure resource lifecycle and protected environment cleanup | Deployment Stacks |
| Multi-cloud deployment or established Terraform practice | Terraform |
| Configuration inside VM operating systems | VM extensions, cloud-init, DSC or configuration-management tool |

- Bicep and Terraform can produce equivalent Azure resources; organizational standards, skills, state-management needs and multi-cloud requirements usually determine the choice
- Azure Policy governs whether deployed resources are compliant; IaC creates resources but does not replace policy enforcement

## Pipeline Integration

- Validate syntax and lint IaC during pull requests
- Preview changes with ARM/Bicep what-if or Terraform plan
- Scan definitions for security and policy violations before deployment
- Require approval for destructive or production changes
- Use workload identity federation and least-privilege deployment identities
- Promote versioned modules and artifacts rather than deploying mutable files from an untrusted location
- Run post-deployment tests and publish deployment evidence

## Environment Design

- Reuse modules while keeping environment-specific parameters separate
- Use separate subscriptions or resource groups for isolation; do not rely only on naming conventions
- Resolve secrets from Key Vault at deployment/runtime instead of checking values into source control
- Treat emergency portal changes as drift: document them and reconcile the source definition promptly
- Avoid one monolithic deployment when components have different owners, lifecycles or permission boundaries

## Exam Design Decisions

- Azure-native language without state management -> **Bicep**
- Multi-cloud infrastructure with shared tooling -> **Terraform**
- Preview Bicep/ARM changes -> **what-if**
- Preview Terraform changes -> **terraform plan**
- Share an approved versioned template with RBAC -> **Template Spec**
- Manage cleanup and protect a resource collection -> **Deployment Stack**
- Enforce compliance regardless of deployment method -> **Azure Policy**
- Remove credentials from the deployment pipeline -> **workload identity federation**

## Related Notes

- [Azure Policy](./azure-policy.md)
- [Governance in Azure](./governance.md)
- [Cloud Adoption Framework](./cloud-adoption-framework.md)
- [Automated Application Deployment](../10-application-architecture/automated-deployment.md)
- [Azure Key Vault](../07-security-in-azure/key-vault.md)