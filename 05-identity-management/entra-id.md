# Microsoft Entra ID (formerly Azure Active Directory - AD)

- Entra ID is the central identity and access management cloud service
- Used to manage access to thousands of apps (among them Azure Portal)
- It is secure, robust, intelligent
- Cloud identity service (HTTP, OAuth, OIDC, SAML) - **not** the same as on-prem AD DS (LDAP, Kerberos, GPOs)

## Tenant

- It is a specific instance of Azure AD containing accounts and groups
- It is called also Directory
- It is not part of the subscription hierarchy:
    - Exists beside subscriptions
    - For new subscriptions, a new tenant is created automatically
- A tenant can be assigned to multiple subscriptions

## Users and Groups

- Two of the main 3 objects managed by Entra ID (3rd one are roles)
- They are used to manage and store users that are part of a tenant
- Groups are used to group Users together
- User types: **Member** (internal) and **Guest** (external, via B2B)
- Dynamic group membership (rules based on attributes) requires Premium P1

## Entra ID Licenses

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

## Entra ID Security Defaults

- Increases protection of the organization in the Free tier
- Adds preconfigured security settings:
    - Require all users to use MFA (blocks 99.9% of account compromises)
    - Block legacy authentication
- No additional cost is required but for more fine-grained management we need to use Conditional Access (requires Premium P1)
- Security Defaults and Conditional Access are **mutually exclusive**

## Conditional Access (P1)

- If-then policies: **signals → decision → enforcement**
- Signals: user/group, app, location, device state, sign-in risk (P2), user risk (P2)
- Decisions: Block, or Grant with conditions (require MFA, compliant device, etc.)
- Use **report-only mode** to test before enforcing

## Privileged Identity Management (PIM) (P2)

- Just-In-Time, time-bound activation of privileged roles
- Users are **eligible** for a role and must **activate** it (optionally with approval + MFA + justification)
- Reduces standing admin access; includes audit logs and access reviews

## Entitlement Management (P2)

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

## Access Reviews (P2)

- Periodic recertification of access to groups, apps, and roles
- Reviewers (admins, resource owners, or the users themselves) attest whether access is still needed
- Stale or unneeded access can be automatically removed; helps enforce least privilege and compliance
- Often combined with **PIM** - Privileged Identity Management (review privileged roles) and **Entitlement Management** (review access packages)

## Role Based Access Control (RBAC)

- In the past authorization was defined per user or user group => this type of authorization is very granular and hard to maintain => RBAC to the rescue
- With RBAC we have 3 elements that define authorization:
    - User
    - Roles
    - Authorizations (privileges)
- Roles are associated to authorizations, users are associated to roles

## RBAC in Azure

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

## Managed Identities

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

## Using Entra ID in our Own App

- Entra ID can be used as authentication engine for custom built applications, not just for the Azure portal
- The process:
    1. Register the app in Entra ID (creates an App registration + Service Principal)
    2. Add code to use Entra ID as authentication engine
        - For App Service: can be configured via Azure Portal (Easy Auth, with minimal code changes)
- The authentication uses OAuth 2.0 / OIDC and JWT tokens
- **Service Principal** = identity used by an app/service to access Azure resources (the in-tenant instance of an App registration)

## Enterprise Applications

- The tenant-level list of applications (Service Principals) that are integrated with Entra ID for SSO and access management
- Two main sources of enterprise apps:
    - **Gallery apps**: thousands of pre-integrated SaaS apps (Salesforce, ServiceNow, Workday, etc.)
    - **Non-gallery / custom apps**: your own apps registered via App registration, or any app supporting SAML/OIDC
- **App Registration vs Enterprise Application**:
    - **App Registration** = the global app *definition* (lives in the home tenant)
    - **Enterprise Application** = the **Service Principal**, the local *instance* of the app in a tenant used for sign-in and access
- Key capabilities:
    - **Single Sign-On (SSO)**: SAML, OIDC, password-based, or linked
    - **User/group assignment**: control who can access the app; optionally require assignment
    - **Provisioning**: automatically create/update/disable user accounts in the SaaS app (SCIM)
    - **Conditional Access**: apply policies (MFA, device compliance, location) per app
    - **Consent and permissions**: manage delegated/application permissions and admin consent

## Azure AD B2C

- It is an identity-as-a-service for our application
- It is a business-to-customer (B2C) service
- It enables integrating identity services in our app
- Provides various user flows and enables customizations for them
- Lives in a **separate tenant** from the workforce Entra ID tenant
- Being superseded by **Microsoft Entra External ID** for new customer scenarios
- Identity services provided by Azure AD B2C:
    - Sign up
    - Sign in
    - Log out
    - Reset password
    - etc.

## Azure AD B2C vs Entra ID

- Entra ID:
    - It is an identity provider => holds users' details
    - It is a single tenant that can be used for authentication by many apps, such as Azure Portal, Office 365, Azure apps
- Azure AD B2C:
    - It is an identity service => used to perform identity-related actions
    - Works with various identity providers, such as Entra ID, Facebook, Google, GitHub, Amazon, Twitter, any OIDC
    - Used by business apps as the identity component

## Azure AD B2C Authentication Features

- Features are:
    - MFA
    - Conditional access
    - Audit log
    - Custom policies
    - Custom pages
- It is quite complex to set up with a lot of moving parts

## B2B Collaboration (Guest Users)

- Invite external users from other tenants or social accounts as **guests**
- Guests authenticate against their **home tenant**; the resource tenant only grants permissions
- Use cases: partner/vendor access, shared documents and apps

## Hybrid Identity — Entra Connect

- Syncs on-premises AD identities to Entra ID so users have a single identity
- AD Connect: it is the agent the facilitates sync-ing between on-prem and Entra ID
- Authentication options (pick one):
    - **Password Hash Sync (PHS)**: hash-of-hash synced to cloud - simplest, default recommendation
    - **Pass-Through Authentication (PTA)**: validation done on-prem via agent - no hashes in cloud
    - **Federation (AD FS)**: redirect auth to on-prem AD FS - most complex, for advanced requirements
- **Seamless SSO** can be added to PHS/PTA for prompt-less sign-in on domain-joined devices

## Entra ID vs AD DS vs Entra Domain Services

- **Entra ID**: cloud identity (modern apps, OAuth/OIDC/SAML); no domain-join, no Group Policy
- **Active Directory Domain Services (AD DS)**: on-prem directory; LDAP, Kerberos, GPO
- **Microsoft Entra Domain Services**: managed AD DS in Azure (domain join, LDAP, Kerberos) for lift-and-shift / legacy apps - no DCs to manage