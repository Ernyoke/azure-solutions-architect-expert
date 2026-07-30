# Active Directory Federation Services (AD FS)

- On-premises **federation** service that provides single sign-on by issuing security tokens (SAML, WS-Federation, OAuth/OIDC)
- In hybrid identity it is one of the three Entra Connect authentication options: **PHS**, **PTA**, **Federation (AD FS)**
- With federation, Entra ID **redirects authentication to the on-premises AD FS farm** - passwords are validated on-prem, never in the cloud
- It is the **most complex and most expensive** option: you own the servers, certificates, patching, and availability

## How Federation Sign-In Works

1. User goes to a cloud app and is redirected to Entra ID
2. Entra ID recognizes the **federated domain** and redirects the user to the AD FS endpoint
3. AD FS authenticates the user against on-premises AD DS (Kerberos/Windows Integrated for domain-joined, forms for external)
4. AD FS issues a signed token; Entra ID validates it and issues its own token to the app

## Typical Components

- **AD FS servers**: issue tokens, hold the token-signing certificate; require AD DS
- **Web Application Proxy (WAP)**: placed in the DMZ, publishes AD FS to the internet without exposing AD FS servers
- **Load balancer** in front of each tier for high availability
- **Certificates**: token-signing, token-decrypting, and SSL/TLS - certificate rollover is a common outage cause

## When Federation Is Still Required

- **Third-party MFA** or smart-card / certificate authentication that Entra ID cannot perform natively
- **On-premises sign-in audit / policy enforcement** that must happen locally
- **Sign-in restrictions** based on on-prem network location handled by AD FS claim rules
- Federation with **third-party identity providers** or existing federated trusts
- Requirement that **no password hashes** are stored in the cloud (PTA also satisfies this, more simply)

## Why Microsoft Recommends Moving Away from AD FS

- Cloud authentication (**PHS** first, then **PTA**) is simpler, cheaper, and removes an on-prem single point of failure
- With federation, an on-prem outage means **users cannot sign in to cloud apps**; PHS keeps cloud sign-in working
- Entra ID features that depend on cloud auth work better: **Identity Protection risk detection, leaked credential detection, smart lockout, Conditional Access**
- Certificate expiry, capacity planning, and patching overhead disappear

## Migrating from AD FS to Cloud Authentication

- Enable **Password Hash Sync** as a backup even while federated (recommended baseline)
- Use **staged rollout** in Entra Connect to move selected groups to PHS or PTA and validate before cutover
- Add **Seamless SSO** so domain-joined devices keep prompt-less sign-in
- Replace AD FS claim rules with **Conditional Access** policies (locations, device state, authentication strength)
- Convert domains from **federated to managed** to complete the cutover
- Use **AD FS application migration** reports (Entra Connect Health / migration wizard) to find apps that can move to Entra ID SSO

## Exam Decision Guide

- Simplest hybrid auth, resilient to on-prem outage → **Password Hash Sync**
- Passwords must be validated on-prem, no hashes in cloud, low complexity → **Pass-Through Authentication**
- Third-party MFA, smart cards, existing federation trusts, on-prem policy enforcement → **AD FS (federation)**
- Reduce on-prem footprint / modernize → migrate AD FS apps to **Entra ID SSO + Conditional Access**

## AD FS vs Entra Domain Services

- **AD FS**: authentication/federation service issuing tokens for apps - about **sign-in**
- **Entra Domain Services**: managed domain offering LDAP/Kerberos/NTLM/domain join in Azure - about **legacy directory protocols**
- They solve different problems and are not alternatives to each other
