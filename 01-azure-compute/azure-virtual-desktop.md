# AVD - Azure Virtual Desktop

- Desktop and application virtualization (VDI / DaaS) running in Azure
- Microsoft manages the **control plane** (connection broker, gateway, web access, diagnostics) for free
- The customer manages the **session hosts** (VMs), images, profiles and networking
- Only place where **Windows 10/11 Enterprise multi-session** is available

## Components

- **Host pool** - a collection of session host VMs
    - **Pooled** - many users share the hosts (multi-session), load balancing is `breadth-first` (spread users) or `depth-first` (fill a host before using the next)
    - **Personal** - one dedicated VM per user (assigned automatically or directly)
- **Application group** - what users get published
    - `Desktop` - full desktop session
    - `RemoteApp` - individual applications only
- **Workspace** - logical grouping of application groups shown in the client
- Users are assigned to application groups via Entra ID users/groups

## Identity requirements

- Session hosts must be joined to:
    - AD DS (with Entra Connect sync), or
    - Microsoft Entra Domain Services, or
    - Microsoft Entra ID join
- Users authenticate with Entra ID, so Conditional Access and MFA apply

## Profiles

- **FSLogix profile containers** store the user profile in a VHD(X)
- Stored on **Azure Files** (Premium for IOPS) or **Azure NetApp Files** for large scale
- Required for pooled host pools so users get their profile on any host

## Scaling and cost

- **Scaling plans** start/stop session hosts based on a schedule and load
- **Start VM on Connect** avoids paying for idle hosts
- Costs: session host VMs + storage + networking (no charge for the AVD service itself)
- Licensing: Windows 10/11 Enterprise rights via Microsoft 365 E3/E5, Windows E3/E5, or per-user access pricing for external users

## Networking and security

- Session hosts live in a customer VNet, connectivity to on-prem via VPN/ExpressRoute
- Reverse connect transport: no inbound ports open on the session hosts
- RBAC for management, Conditional Access for sign-in, Defender for Servers for the hosts
- Trusted launch, disk encryption and Azure Monitor Insights for AVD are supported

## AVD vs Windows 365

- **AVD** - flexible, per-resource pricing, multi-session, full control over images and scaling
- **Windows 365 (Cloud PC)** - fixed per-user per-month price, always-on personal PC, minimal management
