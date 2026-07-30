# Microsoft Entra Domain Services (formerly Azure AD DS)

- Managed domain services in Azure: **domain join, LDAP, Kerberos, NTLM, Group Policy**
- Provides AD DS compatibility **without deploying, patching, or managing domain controllers**
- Target scenario: **lift-and-shift of legacy apps** that cannot use modern authentication (OAuth/OIDC/SAML)
- Microsoft manages the two domain controllers (deployed as a highly available pair), patching, and backups

## How It Works

- A **managed domain** is created and injected into a subnet of an Azure VNet
- Identities are **one-way synchronized from Entra ID → managed domain**
    - Cloud-only users sync directly from Entra ID
    - On-prem users sync to Entra ID first (Entra Connect / Cloud Sync), then into the managed domain
- The sync is **one-way**: changes made in the managed domain are **not** written back to Entra ID or on-prem AD DS
- On-prem passwords require **password hash sync** (legacy NTLM/Kerberos hashes) to be enabled

## Key Characteristics and Limitations

- **No Domain Admin / Enterprise Admin rights** - you get a delegated `AAD DC Administrators` group
- Cannot extend the schema, and cannot create forest trusts except one-way outbound (resource forest, Enterprise SKU)
- **One managed domain per Entra tenant**, deployed into a single VNet in one region
    - Other VNets reach it via VNet peering or VPN/ExpressRoute + correct DNS settings
- Domain name can be the default `<tenant>.onmicrosoft.com` or a custom domain (must not conflict with on-prem)
- Cannot be paused/stopped - billed hourly while it exists
- Secure LDAP (LDAPS) is optional and requires a certificate; avoid exposing it to the internet
- Supports **replica sets** (Premium SKU) for multi-region availability and resiliency

## SKUs

- **Standard**: basic managed domain, no forest trusts
- **Enterprise**: adds forest trusts and replica sets, higher object count
- **Premium**: highest performance and object count, replica sets, for large deployments

## Use Cases

- Windows VMs in Azure that need **domain join** without VPN back to on-prem DCs
- Legacy apps requiring **LDAP bind / LDAP read**, Kerberos or NTLM authentication
- **Group Policy** management for Azure-hosted VMs (default `AADDC Computers` and `AADDC Users` OUs)
- **Azure Virtual Desktop / Azure Files (SMB with Kerberos)** where AD DS identities are required
- Removing the need to run self-managed domain controllers on Azure VMs

## Decision Guide

- App uses modern auth (OAuth/OIDC/SAML) → **Entra ID**
- App needs LDAP/Kerberos/NTLM/GPO **in Azure**, no DC management wanted → **Entra Domain Services**
- Need full control, schema extensions, Domain Admin, two-way trusts → **self-managed AD DS on Azure VMs** (or on-prem)
- Need identities available on-prem **and** in the cloud → **AD DS + Entra Connect/Cloud Sync**

## Comparison

| | Entra ID | Entra Domain Services | AD DS (self-managed) |
|---|---|---|---|
| Protocols | OAuth, OIDC, SAML, HTTP | LDAP, Kerberos, NTLM | LDAP, Kerberos, NTLM |
| Domain join | No (Entra join only) | Yes | Yes |
| Group Policy | No | Yes (limited) | Yes (full) |
| Schema extension | No | No | Yes |
| Managed by | Microsoft | Microsoft | You |
| Trusts | N/A | One-way outbound (Enterprise) | Full forest/domain trusts |
