# Azure Key Vault

- Azure Key Vault helps us safeguard cryptographic keys and other secrets used by cloud apps and services
- Azure Key Vault focuses on 3 things:
    - Certificate Management: easily provision, manage and deploy public and private SSL/TLS certificates for use with Azure and internal connected resources
    - Key Management: create and control the encryption keys used to encrypt data
    - Secrets Management: store tokens, passwords, certificates, API keys and other secrets
- Certificates contain a key pair (key and secret). This should not be confused with Key Management and Secrets Management

## HSM and FIPS

- An HSM is a Hardware Security Module
- It is a piece of hardware designed to store encryption keys
- FIPS: Federal Information Processing Standard
- We have 2 levels of compliance for HSMs:
    - FIPS 140-2 Level 2 Compliant: multiple customers virtually isolated on a shared HSM (used by **Key Vault Premium** tier)
    - FIPS 140-2 Level 3 Compliant: single customer on a dedicated HSM (used by **Azure Managed HSM**, a fully managed, single-tenant pool)

## Vault

- A Vault stores secrets and keys that can be safeguarded by software or FIPS 140-2 L2 validated HSMs
- Azure Key Vaults provides 2 types of containers:
    - Vaults: supports software-protected and HSM-protected keys (HSM-protected requires the Premium tier)
    - Managed HSM pools: only supports HSM-backed keys; fully managed, single-tenant, highly available, FIPS 140-2 Level 3
        - To activate a Managed HSM, we need to:
            - Provide a minimum of three RSA key-pairs (up to max 10)
            - Specify the minimum number of keys required to decrypt a security domain (called quorum)

## Azure Key Vault REST API

- Used to programmatically manage Azure Key Vault resources, allowing to:
    - Create a key or secret
    - Import a key or secret
    - Revoke a key or secret
    - Delete a key or secret
    - Authorize users or apps to access its keys or secrets
    - Monitor and manage key usage
- Azure KV REST API supports 3 different types of authentication:
    - Managed Identities (recommended as best practice)
    - Service Principal and Certificate
    - Service Principal and Secret
- All callers (users and apps) must be authenticated via Microsoft Entra ID before they can access the data plane

## Soft Delete

- Allows us to recover or permanently delete a key vault and secrets for the duration of the retention period
- Retention period is configurable between 7 and 90 days (default 90)
- Enabled by default on creation and cannot be disabled
- Purge protection: when enabled, prevents a soft-deleted vault or object from being permanently purged (even by Microsoft or a subscription admin) until the retention period elapses; once enabled it cannot be turned off

## Pricing

- 2 pricing tiers:
    - Standard and Premium
- Only Premium allows for HSM-protected keys
- Key/secret/certificate **operations** are billed per 10,000 transactions (~$0.03/10k) in both tiers
- HSM-protected keys (Premium) carry an additional monthly per-key charge:
    - Standard HSM keys (RSA 2048): ~$1 per key/month for the first 250 keys, dropping after 250/1500/4000 keys
    - Advanced HSM key types (RSA 3072/4096 and EC): ~$5 per key/month
- Managed HSM is billed per HSM pool **per hour** (not per key)
- Certificate renewals/operations are billed separately

## Double Encryption

- Storage accounts: 
    - By default Azure encrypts storage account data at rest
    - Infrastructure encryption adds a second layer of encryption to the storage account's data
- Azure Disks:
    - Double encryption: two or more independent layers of encryption are enabled to protect agains compromises of any one layer of encryption
- Microsoft has a two layered approach each for data-at-rest and data-in-transit:
    - Data-at-rest:
        1. Disk encryption using customer-managed keys
        2. Infrastructure encryption using platform-managed keys
    - Data in-transit:
        1. TLS 1.2 encryption
        2. Additional layer of encryption provided at the infra layer

## Key Vault - Keys

- When creating a key there are 3 options:
    - Generate: Azure will generate the key for us
    - Import: we can import an existing RSA key
    - Restore Backup: restore a key from backup
- For generated keys we can use:
    - RSA: 2048, 3072, 4096
    - EC: P-256, P-384, P-521, P-256K
- For generated keys we can set an activation and expiration date
- We can create new versions of a key
- Keys support **automated rotation policies** (rotate after N days / before expiry) to meet compliance requirements
- We can download backups of keys. Backups can only be restored within the same Azure subscription and geography (within Azure Key Vault)
- When using a Premium Vault, we have options for HSM:
    - We can generate an RSA/EC key for HSM
    - We can import an RSA key for HSM
- Microsoft managed keys (MMK) are keys managed by Microsoft and they do not appear in our vault. In most cases they are used by Azure services
- Customer managed keys (CMK):
    - Sometimes "customer managed" means that the customer has imported cryptographic material
    - Generated or imported keys are considered CMK in Azure
    - In order to use a key, an Azure service needs an identity (typically a managed identity) with permission to access the key in the vault
    - CMK is used by services such as Storage encryption, SQL TDE, Azure Disk Encryption, and Azure Information Protection

## Key Vault - Secrets

- Provides secure storage of generic secrets such as passwords and database connections strings
- Internally, Key Vault stores and managed secrets:
    - As sequences of octets
    - Each secret with a maximum of 25k bytes
- Key Vault also supports a contentType field for secret, that can be used to assist in interpreting the secret data when retrieved
- Every secret stored in Key Vault is encrypted at rest with a hierarchy of keys:
    - All keys in this hierarchy are protected by modules that are FIPS 140-2 compliant
    - The encryption leaf key is unique to each Key Vault, while the root key is unique to the entire security world
- Secret Attributes:
    - `exp`: expiration time
    - `Nbf`: not before (default value is now) the time before which the secret data should not be retrieved
    - `enabled`: whether the secret can be retrieved
    - Read-only attributes: created, updated

## Access Control (Authorization)

- Key Vault has two planes, each authorized independently:
    - **Management plane**: manage the vault itself (create/delete vaults, set access policies, networking). Always authorized via **Azure RBAC**
    - **Data plane**: work with the data inside the vault (keys, secrets, certificates)
- The data plane supports two permission models:
    - **Azure RBAC (recommended)**: assign built-in roles (e.g. Key Vault Administrator, Key Vault Secrets User, Key Vault Crypto Officer) at management group/subscription/resource group/vault/individual secret scope; centralized, auditable, integrates with PIM
    - **Vault access policies (legacy)**: per-vault list granting specific permissions to a principal; not scoped to individual secrets and harder to manage at scale
- Always authenticate with Microsoft Entra ID; prefer **managed identities** for apps so no credentials are stored

## Networking & Monitoring

- By default the vault is reachable over a public endpoint; access can be restricted with:
    - **Vault firewall**: allow specific public IP ranges / virtual networks
    - **Service Endpoints**: allow trusted subnets over the Azure backbone
    - **Private Endpoint (Private Link)**: assign a private IP inside a VNet, removing public exposure (preferred for production)
    - **Trusted Microsoft services** exception to let select Azure services bypass the firewall
- Logging/monitoring:
    - Enable **diagnostic settings** to send AuditEvent logs to a Log Analytics workspace, Storage, or Event Hub
    - Monitor access and operations via Azure Monitor; set alerts on near-expiry secrets/certificates