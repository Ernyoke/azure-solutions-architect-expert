# Microsoft Entra ID (formerly Azure Active Directory - AD)

- Azure Entry is the central identity and access management cloud service
- Used to manage access to thousands of apps (among them Azure Portal)
- It is secure, robust, intelligent

## Tenant

- It is a specific instance of Azure AD containing accounts and groups
- It is called also Directory
- It is not part of the subscription hierarchy:
    - Exists beside subscriptions
    - For new subscriptions, a new tenant is created automatically
- A tenant can be assigned to multiple subscriptions

## Users and Groups

- Two of the main 3 objectes managed by Azure AD (3rd one are roles)
- They are used to manage and store users that are part of a tenant
- Groups are used to group Users together

## Azure AD Licenses

- Azure AD licenses have great effect on the functionality and price of Azure AD
- Licenses are:
    - Free:
        - Limited to 500_000 of objects
        - MFA support
    - Premium 1:
        - MFA support with Conditional Access
        - Dynamoc groups
        - Conditional Access
        - $6/user/month
    - Premium 2:
        - Risk based conditional access
        - Privileged Identity Management (PIM)
        - $9/user/month

## Azure AD Security Defaults

- Increases protection of the orgnanization in the Free tier
- Adds preconfigured security settings:
    - Require all users to use MFA (blocks 99.9% of account compromises)
    - Block legacy authentication
- No additional cost are required but for more fine-grained management we need to use Conditional Access (requires Premium 1 license)

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
- In Azure in general there are 3 types of roles:
    - Owner: can perform any action on the resources, including assigning roles to it
    - Contributor: can perform any action on the resource, but cannot assign a role to it
    - Reader: can only view the resource, can not change anything

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

## Using Azure AD in our Own App

- Azure AD can be used as authentication engine for custom built applications, not just for the Azure portal
- The process:
    1. Register the app in Azure AD
    2. Add code to use Azure AD as authentication engine
        - For App Service: can be configured via Azure Portal (with minimal code changes)
- The authentication uses Oath and JWT

## Azure AD B2C

- It is an identity-as-a-service for our application
- It is a business-to-customer (B2C) service
- It enables integrating identity services in our app
- Provides various user flows and enables custimzations for them
- Identity services provided by Azure AD B2C:
    - Sign up
    - Sign in
    - Log out
    - Reset password
    - etc.

## Azure AD B2C vs Azure AD

- Azure AD:
    - It is an identity provider => holds users' details
    - It is a single tenant that can be used for authentication by many apps, such as Azure Portal, Office 365, Azure apps
- Azure AD B2C:
    - It is an identity service => used to perfrom identity-related actions
    - Works with various identity providers, such as Azure AD, Facebook, Google, Github, Amazon, Twitter, any OIDC
    - Used by business apps as the identity component

## Azure ADB B2C Authentication Features

- Features are:
    - MFA
    - Conditional access
    - Audit log
    - Custom policies
    - Custom pages
- It is quite complex to set up with a lot of moving parts