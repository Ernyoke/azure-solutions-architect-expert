# Database Security Best Practices

## Network Access

- Use the database **IP firewall rules** to restrict external access to known IP ranges (server-level and database-level rules)
- Disable **"Allow Azure services and resources to access this server"** unless explicitly required
- Connect the DB to the relevant VNet using **Service Endpoints** (backbone routing, free, no on-prem) or **Private Endpoints** (private IP, on-prem support, paid, preferred)
- Prefer **Private Endpoints / Private Link** to remove all public exposure and keep traffic on the Microsoft backbone
- For **SQL Managed Instance**, deploy into a delegated subnet in the VNet (no public endpoint by default)
- Disable the public network access entirely when relying on private connectivity

## Identity & Access

- Use **Microsoft Entra ID authentication** instead of (or alongside) SQL logins; centralizes identity, supports MFA and Conditional Access
- Access the DB from apps using **Managed Identities** so no credentials/connection strings with passwords are stored
- Apply **least-privilege** at the database level (built-in roles, custom roles, row-level security)
- Store any remaining secrets/connection strings in **Azure Key Vault**, not in code or config
- Use a designated **Entra ID admin** for the SQL server/instance

## Data Protection (Encryption)

- **Encryption in transit**: enforce **TLS** (1.2+) for all connections (usually ON by default)
- **Encryption at rest**: **Transparent Data Encryption (TDE)** is enabled by default; use **Customer-Managed Keys (CMK)** in Key Vault (BYOK) for control over key lifecycle/rotation
- Use **Always Encrypted** to protect sensitive columns so data is never exposed in plaintext to the database engine (protects from DBAs/admins)
- Use **Dynamic Data Masking** to limit exposure of sensitive data to non-privileged users
- Use **Row-Level Security (RLS)** to restrict row access based on the querying user

## Threat Protection & Monitoring

- Enable **Microsoft Defender for SQL** (part of Defender for Cloud) for vulnerability assessment and Advanced Threat Protection (SQL injection, anomalous access)
- Enable **SQL Auditing** to a Log Analytics workspace / Storage account for tracking database events
- Follow **Secure Score** and vulnerability assessment recommendations
- Configure **backups** with appropriate retention and geo-redundancy for recovery