# VM Security Best Practices

## Network Access

- Restrict access to the VM as much as possible:
    - Make sure only the required ports are open to the internet
    - Limit access to specific IP addressess when possible
- Use **Network Security Groups (NSGs)** to filter inbound/outbound traffic at the subnet and NIC level
- Prefer using **Azure Bastion** for accessing the VM, so no need for open RDP/SSH ports or public IPs
- Use **Just-in-Time (JIT) VM Access** (Microsoft Defender for Cloud) to open management ports only on request, for a limited time, from approved IPs
- If the VM is not public facing, place it in a VNet that's not connected directly to the internet (no public IP)
- Use a **Jump Box / Bastion host** in a separate subnet for administrative access
- Use **Azure Firewall** or NVAs for centralized egress filtering and forced tunneling

## Identity & Access

- Use **Microsoft Entra ID authentication** for logging into VMs (Linux and Windows) instead of local accounts
- Apply **RBAC** with least-privilege roles to control who can manage the VM
- Use **Managed Identities** so apps on the VM access Azure resources without storing credentials
- Store secrets, keys, and certificates in **Azure Key Vault**, not on the VM
- Enforce **MFA** and **Conditional Access** for administrative access

## Data Protection (Encryption)

- **Encryption at rest** is enabled by default for managed disks (SSE with platform-managed keys)
- Use **Customer-Managed Keys (CMK)** in Key Vault for control over key rotation/lifecycle
- Use **Azure Disk Encryption (ADE)** — BitLocker (Windows) / dm-crypt (Linux) — for OS and data disk volume encryption
- Use **encryption at host** for end-to-end encryption of temp disks and disk caches
- Use **Confidential VMs** for encryption of data in use (memory) for highly sensitive workloads

## Threat Protection & Patching

- Enable **Microsoft Defender for Cloud** (Defender for Servers) for threat detection, vulnerability assessment, and security recommendations
- Follow the **Secure Score** recommendations to improve posture
- Keep the OS and software patched using **Azure Update Manager**
- Use **Microsoft Antimalware** for Windows VMs
- Enable **boot diagnostics** and collect logs for investigation