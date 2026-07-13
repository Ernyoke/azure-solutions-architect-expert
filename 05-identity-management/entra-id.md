# Microsoft Entra ID (formerly Azure Active Directory - AD)

- Entra ID is the central identity and access management cloud service
- Used to manage access to thousands of apps (among them Azure Portal)
- It is secure, robust, intelligent
- Cloud identity service (HTTP, OAuth, OIDC, SAML) - **not** the same as on-prem AD DS (LDAP, Kerberos, GPOs)

## Service Map

- **Microsoft Entra ID**: workforce identity directory, authentication, SSO, application identities, and directory roles
    - Features include tenants, users, groups, authentication methods, Security Defaults, Conditional Access, and Identity Protection
- **Microsoft Entra ID Governance**: governs identity and access lifecycles
    - Features include Privileged Identity Management (PIM), entitlement management, access reviews, and lifecycle workflows
- **Microsoft Entra Workload ID**: protects identities used by applications and services
    - Workload identity types include service principals and managed identities
- **Microsoft Entra External ID**: manages external identities
    - Workforce scenarios use B2B collaboration; customer scenarios use external tenants
- **Microsoft Entra Connect and Cloud Sync**: synchronize identities from AD DS to Entra ID
- **Microsoft Entra Domain Services**: provides managed LDAP, Kerberos, NTLM, domain join, and Group Policy in Azure
- **Azure RBAC**: authorizes access to Azure resources; it is an Azure Resource Manager capability, not an Entra ID feature
- **Managed identities for Azure resources**: Azure-managed workload identities that authenticate through Entra ID

## Microsoft Entra ID - Tenant

- It is a specific instance of Azure AD containing accounts and groups
- It is called also Directory
- It is not part of the subscription hierarchy:
    - Exists beside subscriptions
    - For new subscriptions, a new tenant is created automatically
- A tenant can be assigned to multiple subscriptions
- A subscription trusts exactly one Entra tenant at a time, but it can be transferred to another tenant
- Use a single tenant when possible for simpler administration, collaboration, policy, and licensing
- Use multiple tenants when required by strong isolation, separate legal/regulatory boundaries, or independent administration
- Custom domains provide user-friendly sign-in names; the initial `<tenant>.onmicrosoft.com` domain cannot be removed

## Microsoft Entra ID - Users and Groups

- Two of the main 3 objects managed by Entra ID (3rd one are roles)
- They are used to manage and store users that are part of a tenant
- Groups are used to group Users together
- User types: **Member** (internal) and **Guest** (external, via B2B)
- Dynamic group membership (rules based on attributes) requires Premium P1
- Groups can be **Security groups** (assign access) or **Microsoft 365 groups** (collaboration resources)
- Prefer group-based role and application assignments instead of assigning access directly to individual users
- Administrative units delegate administration over a subset of users, groups, or devices; they are not an Azure resource scope

## Microsoft Entra ID - Authentication Methods

- Prefer passwordless, phishing-resistant authentication over passwords and SMS/voice MFA
- Main options:
    - **Windows Hello for Business**: passwordless sign-in tied to a user and device
    - **FIDO2 security keys / passkeys**: phishing-resistant authentication using public-key cryptography
    - **Certificate-Based Authentication (CBA)**: authenticate with X.509 certificates, useful for regulated and federated environments
    - **Microsoft Authenticator**: passwordless phone sign-in, passkeys, push notifications, and verification codes
    - **Temporary Access Pass (TAP)**: time-limited bootstrap credential used to register passwordless methods or recover access
- Authentication strength in Conditional Access can require specific method combinations, such as phishing-resistant MFA
- **Self-Service Password Reset (SSPR)** lets users reset or unlock passwords after verifying their identity
- With password writeback, cloud password changes are written to on-premises AD DS
- Combined registration lets users register methods once for both MFA and SSPR

## Microsoft Entra ID - Licenses

- Licenses have great effect on the functionality and price of Entra ID
- Licenses are:
    - Free:
        - Limited to 500,000 objects
        - Basic MFA (via Security Defaults)
    - Premium P1 (~$6/user/month):
        - Conditional Access
        - Dynamic groups
        - Self-Service Password Reset (SSPR) with on-prem writeback
        - Application Proxy, Entra Connect
    - Premium P2 (~$9/user/month, includes P1):
        - Risk-based Conditional Access (Identity Protection)
        - Privileged Identity Management (PIM)
        - Access Reviews

## Microsoft Entra ID - Security Defaults

- Increases protection of the organization in the Free tier
- Adds preconfigured security settings:
    - Require all users to use MFA (blocks 99.9% of account compromises)
    - Block legacy authentication
- No additional cost is required but for more fine-grained management we need to use Conditional Access (requires Premium P1)
- Security Defaults and Conditional Access are **mutually exclusive**

## Microsoft Entra ID - Conditional Access (P1)

- If-then policies: **signals → decision → enforcement**
- Signals: user/group, app, location, device state, sign-in risk (P2), user risk (P2)
- Decisions: Block, or Grant with conditions (require MFA, compliant device, etc.)
- Use **report-only mode** to test before enforcing
- Common controls include require an authentication strength, compliant or hybrid-joined device, approved client app, and terms of use
- Policies are evaluated together; access is granted only when all applicable grant requirements are satisfied
- Exclude emergency access (break-glass) accounts from Conditional Access and monitor their use
- Named locations identify trusted countries/regions or IP ranges, but a trusted location alone does not prove user identity
- Continuous Access Evaluation can revoke access to supported apps near real time after critical events such as user disablement or password change

## Microsoft Entra ID - Identity Protection (P2)

- Uses Microsoft signals and machine learning to detect identity risks
- **User risk** indicates the probability that an identity is compromised
- **Sign-in risk** indicates the probability that a specific authentication request is not from the identity owner
- Risk-based Conditional Access can require MFA or a secure password change, or block access
- Risk detections and risky users can be investigated and remediated manually or automatically

## Microsoft Entra ID Governance - Privileged Identity Management (PIM)

- Just-In-Time, time-bound activation of privileged roles
- Users are **eligible** for a role and must **activate** it (optionally with approval + MFA + justification)
- Reduces standing admin access; includes audit logs and access reviews

## Microsoft Entra ID Governance - Entitlement Management

- Part of **Microsoft Entra ID Governance**; automates the access lifecycle at scale
- Solves the problem of managing access to many resources (groups, apps, SharePoint sites) for internal and external users
- Core building blocks:
    - **Access Package**: a bundle of resources (groups, apps, SharePoint sites) plus the roles a user gets on them
    - **Catalog**: a container that groups related resources and access packages; can be delegated to non-admin owners
    - **Policy**: defines *who* can request a package, the *approval* workflow, and *lifecycle* settings (expiration, access reviews)
- Self-service access requests: users request an access package and get access automatically after approval
- Supports **external users (B2B)** — guests can request packages and are auto-removed when access expires
- Lifecycle automation: time-bound access, **automatic expiration**, and recurring **access reviews** to remove stale access
- **Separation of Duties (SoD)**: block a user from requesting a package if they already have a conflicting one

## Microsoft Entra ID Governance - Access Reviews

- Periodic recertification of access to groups, apps, and roles
- Reviewers (admins, resource owners, or the users themselves) attest whether access is still needed
- Stale or unneeded access can be automatically removed; helps enforce least privilege and compliance
- Often combined with **PIM** - Privileged Identity Management (review privileged roles) and **Entitlement Management** (review access packages)

## Authorization - RBAC Concepts

- In the past authorization was defined per user or user group => this type of authorization is very granular and hard to maintain => RBAC to the rescue
- With RBAC we have 3 elements that define authorization:
    - User
    - Roles
    - Authorizations (privileges)
- Roles are associated to authorizations, users are associated to roles
- A role assignment consists of **security principal + role definition + scope**
- Security principals can be users, groups, service principals, or managed identities
- Apply least privilege and assign at the narrowest practical scope; broader assignments are inherited by child scopes
- **Deny assignments** block actions even when a role assignment grants them; they are usually created and managed by Azure

## Azure RBAC

- In order to perform any operation or access any data in Azure, we need to have the appropriate role
- When a new subscription is created, we are granted with the Owner role (basically allows everything)
- Main built-in roles:
    - Owner: can perform any action on the resources, including assigning roles to it
    - Contributor: can perform any action on the resource, but cannot assign a role to it
    - Reader: can only view the resource, can not change anything
    - User Access Administrator: manages access to Azure resources only
- Role scope hierarchy (inherited downward): **Management Group → Subscription → Resource Group → Resource**
- **Azure RBAC roles** (resources) and **Entra ID roles** (e.g., Global Administrator) are separate role systems
- Custom roles can be created when built-in roles don't fit
- Azure RBAC separates **control-plane actions** (`Actions`) from **data-plane access** (`DataActions`)
- A Contributor can manage a storage account but cannot necessarily read its blobs; assign an appropriate data role such as Storage Blob Data Reader
- Entra roles manage directory resources such as users, groups, applications, and licenses
- Prefer task-specific Entra roles (such as User Administrator or Application Administrator) over Global Administrator
- A Global Administrator does not automatically manage Azure resources; the administrator can elevate access temporarily to User Access Administrator at the root scope

## Microsoft Entra Workload ID

- A workload identity is an identity for an application, service, script, container, or other non-human workload
- Main choices:
    - **Managed identity**: best for supported Azure resources; Azure manages the credential lifecycle
    - **Service principal with workload identity federation**: best for GitHub Actions, Azure DevOps, Kubernetes, or external workloads; exchanges a trusted OIDC token without storing a secret
    - **Service principal with certificate**: use when federation or managed identity is unavailable; certificates are preferred over client secrets
    - **Service principal with client secret**: least preferred because secrets must be stored, rotated, and protected
- Apply least-privilege Azure RBAC or API permissions to workload identities and monitor service principal sign-ins
- Conditional Access for workload identities can restrict service principals by location or risk; managed identities are not covered

## Managed Identities for Azure Resources

- Provide the ability to assign Azure AD identity to Azure resources, so these resources can connect to other Azure resources using the identity
- Using managed identities there is no need to manage credentials (usernames, passwords, etc.)
- There are 2 types of managed identities:
    - System assigned: managed by Azure, tied to the resource's lifecycle => when a resource is deleted - so is the identity
    - User assigned: managed by the user, can be be assigned to multiple resources, not tied to any lifecycle
- Resources that can be assigned managed identities:
     - App Service
     - Virtual Machine
     - Event Grid
     - Function
     - etc.
- Resources that can be authorized using managed identities:
    - SQL
    - Event Hubs
    - Service Bus
    - Storage
    - Key Vault

## Microsoft Entra ID - App Registrations

- Entra ID can be used as authentication engine for custom built applications, not just for the Azure portal
- The process:
    1. Register the app in Entra ID (creates an App registration + Service Principal)
    2. Add code to use Entra ID as authentication engine
        - For App Service: can be configured via Azure Portal (Easy Auth, with minimal code changes)
- The authentication uses OAuth 2.0 / OIDC and JWT tokens
- **Service Principal** = identity used by an app/service to access Azure resources (the in-tenant instance of an App registration)
- Supported account type determines whether the app is **single-tenant**, **multitenant**, or also accepts personal Microsoft accounts
- OAuth permission types:
    - **Delegated permissions**: the app acts on behalf of a signed-in user; effective access is limited by both the user and app permissions
    - **Application permissions**: the app acts as itself without a user; normally requires administrator consent
- **App roles** are application-defined roles emitted in tokens and can be assigned to users, groups, or service principals
- **Scopes** define delegated permissions that a client can request for an API
- User consent can grant low-impact delegated permissions; admin consent is required for high-impact or application permissions
- Prefer Microsoft Authentication Library (MSAL) and authorization code flow with PKCE for user-facing apps
- Use client credentials flow only for daemon/service applications without a signed-in user

## Microsoft Entra ID - Enterprise Applications

- **Enterprise Applications** is the Entra portal area used to manage applications that have an identity in the current tenant
- Each enterprise application is represented by a **service principal**: the local instance of an application in a tenant
- An enterprise application can be created when:
    - An administrator adds a pre-integrated application from the Entra application gallery, such as Salesforce or ServiceNow
    - An administrator adds a non-gallery application for SAML or other SSO integration
    - An application is registered in the tenant
    - A user or administrator consents to a multitenant application from another tenant
    - Azure creates a managed identity for an Azure resource
- Administrators use the enterprise application to configure the application's tenant-specific behavior:
    - **Single Sign-On (SSO)**: configure SAML, OIDC, password-based, or linked sign-in
    - **Users and groups**: assign who can sign in and optionally require assignment
    - **Provisioning**: create, update, and disable accounts in the target application, commonly through SCIM
    - **Conditional Access**: apply controls such as MFA, device compliance, and location restrictions
    - **Permissions and consent**: review delegated or application permissions and grant administrator consent
    - **Monitoring**: review sign-in activity, audit logs, and provisioning logs
- **App registration vs enterprise application**:
    - **App registration**: the application definition used by developers to configure redirect URIs, credentials, API scopes, and app roles
    - **Enterprise application**: the service principal used by tenant administrators to configure access, SSO, provisioning, consent, and policies
- Example: a multitenant SaaS provider has one app registration in its home tenant, while each customer tenant gets its own enterprise application when the app is added or consented to

## Microsoft Entra ID - Application Proxy

- Publishes on-premises web applications for remote access through Entra ID without opening inbound firewall ports
- Uses outbound connections from lightweight on-premises connectors to the Application Proxy service
- Adds Entra preauthentication, Conditional Access, MFA, and SSO in front of legacy applications
- Supports SSO methods including Kerberos Constrained Delegation, header-based, password vaulting, and SAML
- Choose Application Proxy for web applications; use VPN or private connectivity when users require broader network-level access

## Microsoft Entra External ID - Customer Identity

- Provides identity and access management for customer-facing applications
- Uses an **external tenant**, separate from the workforce tenant used by employees and Azure administrators
- Supports sign-up, sign-in, sign-out, password reset, MFA, audit logs, and customizable user flows and pages
- Can federate with social and enterprise identity providers that support protocols such as OIDC and SAML
- **Microsoft Entra External ID** is the current platform for new customer identity scenarios
- **Azure AD B2C** is the previous customer identity product and is no longer available to purchase for new customers as of May 1, 2025

## Microsoft Entra External ID - B2B Collaboration

- Invite external users from other tenants or social accounts as **guests**
- Guests authenticate against their **home tenant**; the resource tenant only grants permissions
- Use cases: partner/vendor access, shared documents and apps
- Cross-tenant access settings control inbound and outbound B2B collaboration and direct connect between organizations
- Configure trust settings to accept MFA and device claims from a partner tenant and avoid duplicate authentication
- Cross-tenant synchronization automates creation, update, and removal of B2B users across tenants
- Use entitlement management when external access needs requests, approvals, expiration, and access reviews

## Microsoft Entra Connect and Cloud Sync

- Syncs on-premises AD identities to Entra ID so users have a single identity
- **Microsoft Entra Connect Sync** uses a synchronization server and supports advanced synchronization, more object types, and complex hybrid scenarios
- **Microsoft Entra Cloud Sync** uses lightweight cloud-managed agents and is suitable for multiple disconnected forests and simpler synchronization requirements
- Authentication options (pick one):
    - **Password Hash Sync (PHS)**: hash-of-hash synced to cloud - simplest, default recommendation
    - **Pass-Through Authentication (PTA)**: validation done on-prem via agent - no hashes in cloud
    - **Federation (AD FS)**: redirect auth to on-prem AD FS - most complex, for advanced requirements
- **Seamless SSO** can be added to PHS/PTA for prompt-less sign-in on domain-joined devices
- Source of authority matters: synchronized attributes are generally managed on-premises, while cloud-only objects are managed in Entra ID
- Use **staged rollout** to test cloud authentication methods for selected groups before moving away from federation
- Deploy multiple PTA agents for high availability; PHS also provides cloud authentication resilience if on-premises services are unavailable
- **Microsoft Entra Kerberos** enables supported cloud identities to access on-premises Kerberos resources, including with Windows Hello for Business cloud Kerberos trust

## Services for Access to On-Premises Resources

- Keep AD DS when applications require LDAP, Kerberos, NTLM, Group Policy, or traditional domain join
- Hybrid users can use their synchronized identity to access both cloud and on-premises resources
- Use Microsoft Entra private access for identity-aware Zero Trust access to private TCP applications and resources without providing broad network access
- Use Application Proxy for private web applications that need Entra preauthentication and Conditional Access
- Use VPN or ExpressRoute when applications require general network connectivity or protocols not supported by identity-aware application access
- Microsoft Entra Domain Services provides managed LDAP, Kerberos, NTLM, domain join, and Group Policy compatibility in Azure without managing domain controllers

## Microsoft Entra ID Governance - Lifecycle Workflows

- Lifecycle workflows automate joiner, mover, and leaver tasks based on user attributes and employment events
- Built-in tasks can enable or disable accounts, add or remove group or access package assignments, and notify users or managers
- Workflows can run automatically from employee attributes or on demand for selected users
- Use entitlement management for access requests and expiration, access reviews for recertification, and PIM for privileged access

## Microsoft Entra ID - Provisioning and Logs

- Application provisioning uses standards such as **SCIM** to create, update, and disable accounts in SaaS applications
- **Audit logs** record directory and configuration changes
- **Sign-in logs** record user, service principal, and managed identity authentication activity
- **Provisioning logs** record identity synchronization and application provisioning activity
- **Terms of use** records user acceptance and can be required by Conditional Access

## Identity Design Decisions

- **Employees and administrators**: use workforce Entra ID, phishing-resistant MFA, Conditional Access, least privilege, and PIM
- **Business partners**: use B2B collaboration; add cross-tenant access settings and entitlement management for governed access
- **Customers and consumers**: use Microsoft Entra External ID in an external tenant
- **Azure-hosted workload**: use a managed identity when the service supports it
- **External CI/CD or Kubernetes workload**: use workload identity federation instead of stored credentials
- **Legacy Azure-hosted application requiring domain protocols**: use Microsoft Entra Domain Services
- **Existing on-premises domain workload**: retain AD DS and synchronize identities with Cloud Sync or Connect Sync
- **On-premises web application for remote users**: use Application Proxy with Conditional Access
- **Azure resource authorization**: use Azure RBAC; for directory administration use Entra roles; for application authorization use app roles/scopes
- Centralize identity where possible, but use separate tenants when legal, regulatory, or administrative isolation outweighs collaboration overhead

## Entra ID vs AD DS vs Entra Domain Services

- **Entra ID**: cloud identity (modern apps, OAuth/OIDC/SAML); no domain-join, no Group Policy
- **Active Directory Domain Services (AD DS)**: on-prem directory; LDAP, Kerberos, GPO
- **Microsoft Entra Domain Services**: managed AD DS in Azure (domain join, LDAP, Kerberos) for lift-and-shift / legacy apps - no DCs to manage