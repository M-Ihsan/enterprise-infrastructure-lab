# Enterprise Infrastructure & Cloud Security — Practical Lab Notebook

**November 2026**

> Consolidated reference notebook covering networking foundations through advanced Group Policy and IPv6 fundamentals. This summarizes concepts across the detailed per-stage files in this repo (`01-networking/` through `05-group-policy/`) — read those for full step-by-step procedures and verification logs; this file is the condensed study reference.

## Table of contents

1. Networking foundations
2. Windows Server administration
3. Active Directory fundamentals
4. Organizational Units, users and security groups
5. File sharing and permissions
6. Domain-joined computers
7. Group Policy fundamentals
8. Advanced Group Policy
9. IPv4 and IPv6
10. Lab topology
11. Domain password & account lockout policy
12. Fine-Grained Password Policies
13. Windows security hardening (initial assessment)

---

## Chapter 1 — Networking foundations

### 1.1 What is a computer network?

A collection of connected devices that communicate and share resources — computers, servers, printers, routers, switches.

### 1.2 OSI model

| Layer | Name | Function |
|---|---|---|
| 7 | Application | Network services used by applications |
| 6 | Presentation | Data formatting and encryption |
| 5 | Session | Manages communication sessions |
| 4 | Transport | TCP and UDP communication |
| 3 | Network | IP addressing and routing |
| 2 | Data Link | MAC addresses and frames |
| 1 | Physical | Cables, signals, physical transmission |

### 1.3 TCP/IP

- **TCP:** reliable, connection-oriented communication
- **UDP:** connectionless, lower overhead

### 1.4 IPv4

32-bit addresses, e.g. `192.168.50.10`.

```
Network:       192.168.50.0/24
Subnet mask:   255.255.255.0
DC01:          192.168.50.10
CLIENT01:      192.168.50.20
```

### 1.5 Static vs DHCP

- **Static IP:** manually assigned address
- **DHCP:** automatically assigns network configuration

Servers commonly use stable (static) addresses so other devices can reliably locate them.

### 1.6 DNS

Translates hostnames to IP addresses and helps clients locate network services.

```
DC01.technova.test  →  192.168.50.10
```

### 1.7 IPv6

128-bit addresses, e.g. `2001:db8:50::10`.

| Record | Purpose |
|---|---|
| A | IPv4 address |
| AAAA | IPv6 address |
| SRV | Locates network services |
| PTR | Reverse DNS lookup |

AD fully supports IPv6. This lab uses IPv4 for simplicity — IPv6 should never be disabled just because IPv4 is configured.

---

## Chapter 2 — Windows Server administration

### 2.1 What is Windows Server?

Microsoft's server OS — network services, centralized administration, identity management, enterprise infrastructure.

### 2.2 DC01 — lab configuration

| Component | Configuration |
|---|---|
| OS | Windows Server 2012 R2 Standard |
| Installation | Desktop Experience |
| Hypervisor | VMware Workstation |
| RAM | 2 GB |
| Hostname | DC01 |
| IPv4 | 192.168.50.10 |
| Network | Host-only VMnet2 |
| Domain | technova.test |

### 2.3 Key concepts

- **Hostname:** name identifying a computer
- **Server role:** a major function performed by a server
- **Feature:** an additional Windows capability
- **Static IP:** manually configured IP address
- **Host-only network:** private virtual network between host and lab VMs

### 2.4 Basic commands

```cmd
hostname            REM computer name
ipconfig /all        REM network configuration
ping 192.168.50.10   REM test IP connectivity
nslookup technova.test REM test DNS resolution
```

---

## Chapter 3 — Active Directory fundamentals

### 3.1 What is Active Directory?

Microsoft's directory service for centrally managing identities, computers, groups and access within a Windows domain.

### 3.2 What is AD DS?

The Windows Server role providing directory services and domain authentication.

### 3.3 Key terminology

| Term | Explanation |
|---|---|
| Domain | Administrative boundary for network identities |
| Domain Controller | Server providing domain authentication and directory services |
| Forest | Top-level AD structure |
| Tree | Hierarchical collection of domains |
| OU | Organizational Unit used to organize AD objects |
| Global Catalog | Helps locate directory objects across a forest |
| DSRM | Directory Services Restore Mode |
| SYSVOL | Shared location for domain policies and scripts |
| SID | Security Identifier for a security principal |
| LDAP | Protocol used to access directory information |
| Kerberos | Network authentication protocol |
| NTLM | Windows authentication protocol |

### 3.4 Our domain

```
Domain:       technova.test
NetBIOS:      TECHNOVA
Domain DC:    DC01
DC IPv4:      192.168.50.10
```

### 3.5 AD DS install vs. promotion

Two separate processes:

1. Install the AD DS server role.
2. Promote the server to a Domain Controller (creates the forest/domain).

### 3.6 Verification commands

```powershell
Get-ADDomain                     # domain information
Get-ADForest                     # forest information
Get-ADDomainController -Filter * # list domain controllers
Get-Service NTDS                 # AD DS service
Get-Service DNS                  # DNS service
```

```cmd
dcdiag       REM domain controller diagnostics
```

---

## Chapter 4 — Organizational Units, users and security groups

### 4.1 What is an OU?

A logical container inside AD, used to organize objects and apply policies.

```
technova.test
│
└── TechNova
    ├── Employees
    ├── Departments
    │   ├── HR
    │   ├── IT
    │   ├── Finance
    │   └── Management
    ├── Computers
    └── Security Groups
```

### 4.2 What is a user account?

Represents a person's digital identity, authenticating to the domain and accessing authorized resources (e.g. Sarah, Ali, Emma, Michael — fictional test accounts).

### 4.3 What is a security group?

A collection of accounts used to assign permissions.

| Group | Department |
|---|---|
| GG_HR | HR |
| GG_IT | IT |
| GG_Finance | Finance |
| GG_Management | Management |

### 4.4 Group scope

- **Global:** commonly collects users from the same domain
- **Domain Local:** commonly used to assign permissions to resources in a domain
- **Universal:** can contain members from different domains in a forest

### 4.5 Group type

- **Security group:** used for permissions
- **Distribution group:** used for email distribution

### 4.6 Why use groups?

Assign permissions to groups, not individuals — much easier when employees join, leave, or change departments.

---

## Chapter 5 — File sharing and permissions

### 5.1 What is SMB?

Server Message Block — protocol for sharing files/folders over a network.

### 5.2 Network share

Allows authorized computers to access a folder over the network, e.g. `\\DC01\HR`.

### 5.3 Folder structure

```
C:\CompanyData
│
├── HR
├── IT
├── Finance
└── Management
```

### 5.4 NTFS permissions

| Permission | Meaning |
|---|---|
| Read | View files and folders |
| Write | Create or modify files |
| Modify | Read, write and delete |
| Full Control | Includes permission management |

### 5.5 Share permissions

Control access through the network share itself (separate layer from NTFS).

### 5.6 Security principals used

| Principal | Purpose |
|---|---|
| SYSTEM | Windows operating system account |
| Administrators | Administrative access |
| CREATOR OWNER | Special identity for creator-related permissions |
| Department security group | Department-specific access |

### 5.7 Effective permission principle

Effective network access depends on **both** SMB share permissions and NTFS permissions — the more restrictive applicable permission (evaluated together, not one simply overriding the other) determines actual access.

### 5.8 Least privilege

Give users only the permissions required to do their work.

---

## Chapter 6 — Domain-joined computers

### 6.1 What is a domain-joined computer?

Connected to an AD domain, able to use domain identities and centrally managed policies.

### 6.2 CLIENT01 configuration

```
Computer name: CLIENT01
IPv4:          192.168.50.20
Subnet mask:   255.255.255.0
Preferred DNS: 192.168.50.10
Domain:        technova.test
```

### 6.3 Why DC01 as DNS?

The client needs DC01's DNS to locate the Domain Controller and AD services (SRV records, etc.).

### 6.4 Domain authentication

Example: `TECHNOVA\Sarah` — Sarah authenticating with an account in the TECHNOVA domain.

### 6.5 Key concepts

- **Domain join:** connects a computer to an AD domain
- **Computer account:** represents a computer in AD
- **Authentication:** verifies identity
- **Authorization:** determines what an authenticated identity can access

---

## Chapter 7 — Group Policy fundamentals

### 7.1 What is Group Policy?

Centrally manages user and computer configuration.

### 7.2 What is a GPO?

A Group Policy Object — a collection of policy settings.

### 7.3 GPMC

Group Policy Management Console — the interface for creating, editing, linking and managing GPOs.

### 7.4 User vs. Computer Configuration

| User Configuration | Computer Configuration |
|---|---|
| Follows the user | Follows the computer |
| User-specific settings | Machine-specific settings |
| Can apply on different computers | Applies according to computer policy scope |

### 7.5 HR drive-mapping policy

```
GPO:       TN-HR-Drive-Mapping
OU:        HR
Drive:     H:
Location:  \\DC01\HR
Label:     HR Shared
```

### 7.6 Logon message policies

```
GENERAL: TechNova Ltd. — Employee Notice
HR:      TechNova Ltd. — HR Department
```

Computer-based policies — the computer's OU determines which settings it receives, regardless of the logged-in user's department.

### 7.7 Computer OU structure

```
TechNova
└── Computers
    ├── Workstations
    │   ├── HR-Workstations
    │   ├── IT-Workstations
    │   ├── Finance-Workstations
    │   ├── Management-Workstations
    │   └── Shared-Workstations
    │
    └── Servers
```

### 7.8 Group Policy drive mapping

Network folders appear as drive letters in File Explorer, e.g. `H: = \\DC01\HR`.

---

## Chapter 8 — Advanced Group Policy

### 8.1 LSDOU processing order

| Order | Level |
|---|---|
| 1 | Local |
| 2 | Site |
| 3 | Domain |
| 4 | OU |
| 5 | Child OU |

### 8.2 GPO link order

Multiple GPOs linked to the **same** container → link order 1 has the highest precedence among those links.

### 8.3 Inheritance

Child OUs normally inherit applicable GPOs from parent containers — **different mechanism from link order** (link order only applies within one container; inheritance is about parent→child processing).

### 8.4 Block Inheritance

Prevents normal inheritance of GPOs from higher-level containers.

### 8.5 Enforced

An enforced GPO link can't normally be blocked by inheritance blocking, and takes precedence over conflicting lower-level settings.

### 8.6 Security Filtering

Controls which users/computers have permission to apply a GPO.

### 8.7 Group Policy Loopback

Lets user-policy processing be influenced by the computer's location — useful for shared workstations, reception computers, training rooms, kiosks.

### 8.8 Troubleshooting commands

```cmd
gpupdate /force                     REM refresh Group Policy
gpresult /r                         REM display applied policies
gpresult /h C:\gpo-report.html      REM detailed HTML report
```

```powershell
Get-GPO -All   # list all GPOs
```

---

## Chapter 9 — IPv4 and IPv6

### 9.1 IPv4

32-bit, dotted decimal, e.g. `192.168.50.10`.

### 9.2 IPv6

128-bit, hexadecimal, e.g. `2001:db8:50::10`.

### 9.3 Why IPv6?

- Vastly larger address space
- Modern address configuration
- Better support for large-scale networks
- Less dependence on NAT

### 9.4 Does AD support IPv6?

Yes — both AD and DNS support IPv6.

### 9.5 Key terms

| Term | Meaning |
|---|---|
| SLAAC | Stateless Address Autoconfiguration |
| DHCPv6 | Dynamic IPv6 configuration |
| AAAA | IPv6 DNS record |
| A | IPv4 DNS record |
| Dual Stack | Running IPv4 and IPv6 together |

**Remember:** IPv4 and IPv6 can run together. Never disable IPv6 just because the lab currently uses IPv4.

---

## Chapter 10 — Lab Topology

```
             TECHNOVA.TEST
                   │
             DC01 (Server)
             192.168.50.10
             AD DS + DNS
                   │
             VMware VMnet2
             Host-Only Network
                   │
             CLIENT01 (Win 10)
             192.168.50.20
                   │
          Domain User Accounts
                   │
          Department File Shares
          HR | IT | Finance | Management
```

---

## Chapter 11 — Domain Password & Account Lockout Policy

### 11.1 Domain Password Policy

Location: `Default Domain Policy → Computer Configuration → Policies → Windows Settings → Security Settings → Account Policies → Password Policy`

| Policy | Lab value |
|---|---|
| Enforce password history | 5 passwords |
| Maximum password age | 90 days |
| Minimum password age | 1 day |
| Minimum password length | 12 characters |
| Complexity requirements | Enabled |
| Reversible encryption | Disabled |

- **History** prevents immediate password reuse.
- **Min age** stops rapid changes used to bypass history.
- **Modern note:** long unique passwords, MFA, and compromised-password protection matter more than forced periodic rotation alone — rotation policy should follow organizational risk, not be applied as a blanket "best practice."

### 11.2 Account Lockout Policy

Location: same path, **Account Lockout Policy**.

| Policy | Lab value |
|---|---|
| Lockout threshold | 5 invalid attempts |
| Lockout duration | 15 minutes |
| Reset counter after | 15 minutes |

Never intentionally trigger lockouts during verification — verify via `Get-ADDefaultDomainPasswordPolicy` instead.

### 11.3 Important distinction

The Default Domain Policy is the normal place for domain-wide password/lockout settings. A password policy linked only to an ordinary OU generally does **not** change the domain password policy for domain accounts — that's what Fine-Grained Password Policies (FGPP) are for.

---

## Chapter 12 — Fine-Grained Password Policies (FGPP)

### 12.1 Why FGPP

Lets a stricter password policy apply to selected users/groups (e.g. IT admins) without changing the policy for the whole domain. Implemented via **Password Settings Objects (PSOs)**.

### 12.2 Group used

`GG_IT_Admins` — created under `TechNova → Security Groups`, global security group.

**Distinction:** `GG_IT` = department membership. `GG_IT_Admins` = selected accounts for this policy exercise — membership doesn't grant actual Windows admin rights, it's just the PSO assignment target. A user can belong to both groups.

### 12.3 PSO — `PSO_IT_Admins`

Created via Active Directory Administrative Center → `technova (Local)` → **System** → **Password Settings Container**.

| Policy | Value |
|---|---|
| Precedence | 10 |
| Minimum password length | 14 |
| Password history | 10 |
| Complexity | Enabled |
| Reversible encryption | Disabled |
| Min/max password age | 1 day / 90 days |
| Lockout threshold | 5 |
| Lockout duration / reset counter | 15 min / 15 min |

Assigned to `GG_IT_Admins` under **Directly Applies To**. FGPP applies directly to users or global security groups — never to OUs.

### 12.4 Verification commands

```powershell
Get-ADFineGrainedPasswordPolicy -Filter *
Get-ADFineGrainedPasswordPolicySubject -Identity PSO_IT_Admins
Get-ADUserResultantPasswordPolicy -Identity <username>
```

### 12.5 Precedence

Lower number = higher priority (10 beats 20). Tie-breaking rules apply if two PSOs share the same precedence for the same subject.

| Standard Domain Policy | FGPP |
|---|---|
| Domain-wide baseline | Targeted policy |
| Configured via Group Policy | Configured via PSOs |
| Applies to domain accounts by default | Targets users or global security groups |
| Not OU-specific | Not OU-specific |

---

## Chapter 13 — Windows Security Hardening (Initial Assessment)

> Initial assessment only — not a complete hardening deployment.

### 13.1 Windows Firewall basics

- **Inbound:** traffic coming in. **Outbound:** traffic leaving.
- Profiles: **Domain** (domain-authenticated network), **Private** (trusted), **Public** (untrusted, e.g. public Wi-Fi).
- A DC should correctly identify its domain network and use the Domain profile.

### 13.2 Firewall profile check

```powershell
Get-NetFirewallProfile | Format-Table Name, Enabled, DefaultInboundAction, DefaultOutboundAction
```

All 3 profiles: `Enabled = True`. `NotConfigured` on the action columns ≠ firewall disabled — just means that policy store doesn't explicitly override the OS's built-in default behavior.

### 13.3 Enabled rule counts

```powershell
Get-NetFirewallRule -PolicyStore ActiveStore -Enabled True -Direction Inbound | Group-Object Action | Select-Object Name, Count
Get-NetFirewallRule -PolicyStore ActiveStore -Enabled True -Direction Outbound | Group-Object Action | Select-Object Name, Count
```

Result: 68 enabled inbound Allow rules, 36 enabled outbound Allow rules — expected for a DC (AD DS/DNS/Kerberos/SMB/RPC need these open). **Scope note:** this only counts enabled Allow rules — not a full rule-by-rule audit, Block rules weren't individually inspected.

### 13.4 Network Location Awareness (NLA) issue — resolved

DC01 initially showed `NetworkCategory: Public` / "Unidentified network" instead of `DomainAuthenticated`, despite AD services running fine. Root cause: host-only VMnet with no default gateway can confuse NLA's network classification even when domain auth itself works.

Fix: `Restart-Service NlaSvc` failed (dependent services) → `Restart-Service -Name NlaSvc -Force` succeeded → `NetworkCategory` corrected to `DomainAuthenticated`.

**Lesson:** don't force the Domain profile manually or randomly disable services to chase a network-identification issue — find and fix the actual cause.

### 13.5 DNS verification

```powershell
Get-Service DNS                                                                      # Running
Resolve-DnsName dc01.technova.test -Server 192.168.50.10                             # A: 192.168.50.10, AAAA: fe80::... (link-local, expected)
Resolve-DnsName _ldap._tcp.dc._msdcs.technova.test -Type SRV -Server 192.168.50.10    # dc01.technova.test : 389
Resolve-DnsName _kerberos._tcp.dc._msdcs.technova.test -Type SRV -Server 192.168.50.10 # dc01.technova.test : 88
```

All internal AD DNS confirmed healthy.

### 13.6 DNS diagnostic — root hints failure (expected, not a bug)

`dcdiag /test:dns /v` reported root-hints/forwarders failure — all 13 external root servers unreachable. **Expected** in a host-only lab network with no default gateway/internet route; root hints are for external DNS resolution, irrelevant to AD's own function. Don't add forwarders just to make this test pass.

PTR timeout for `127.0.0.1` noted but not fully investigated — not claimed as resolved.

### 13.7 Security considerations

- Keep Windows Firewall enabled; don't randomly disable DC firewall rules.
- AD depends on DNS, LDAP, Kerberos, SMB, RPC all staying open appropriately.
- Validate network/DNS before changing firewall policy.
- Keep IPv6 enabled unless a specific documented reason says otherwise.
- Server 2012 R2 is unsupported — isolated lab only, never production.

### 13.8 Checkpoint

**Completed:** firewall profiles identified/verified enabled; enabled Allow rules inspected; network identification corrected (Public → DomainAuthenticated); DC hostname, LDAP SRV, Kerberos SRV all verified; external DNS diagnostic failure correctly attributed to lab network isolation.

**Not completed (future modules):** full firewall-rule analysis, endpoint protection assessment, security auditing configuration, event-log investigation, advanced hardening.

---

## Quick command reference

| Command | Purpose |
|---|---|
| `hostname` | Display computer name |
| `ipconfig /all` | Show network configuration |
| `ping <IP>` | Test connectivity |
| `nslookup <domain>` | Test DNS |
| `dcdiag` | Diagnose Domain Controller |
| `Get-ADDomain` | Display AD domain |
| `Get-ADForest` | Display AD forest |
| `Get-ADUser -Filter *` | List AD users |
| `Get-ADGroup -Filter *` | List AD groups |
| `Get-GPO -All` | List GPOs |
| `gpupdate /force` | Refresh policies |
| `gpresult /r` | Show applied policies |
| `gpresult /h C:\gpo-report.html` | Create policy report |
| `Get-Service NTDS` | Check AD DS service |
| `Get-Service DNS` | Check DNS service |

---

## Current infrastructure snapshot

**TechNova Ltd. — Active Directory training environment**

- **DC01** — Windows Server 2012 R2 · AD DS · DNS · `192.168.50.10` · `technova.test`
- **HR / IT / Finance / Management** — users and groups configured
- **Department SMB shares** — HR, IT, Finance, Management
- **CLIENT01** — domain-joined Windows client · `192.168.50.20`
- **Policies implemented:** department drive mapping, general workstation notice, HR workstation notice, GPO inheritance/precedence experiments

## Roadmap — what's next

| Module | Practical focus |
|---|---|
| Password & account security | Password policy, lockout, fine-grained policies |
| Windows hardening | Defender, Firewall, LAPS |
| Auditing | Security logs, audit policies, Sysmon |
| PowerShell | Automating AD administration |
| AD security | Privileges, delegation, permissions |
| Entra ID | Cloud identities and access |
| Hybrid identity | On-prem AD + cloud synchronization |
| Azure | Infrastructure and cloud security |

## Notes

Detailed step-by-step procedures, screenshots, and verification logs for each stage live in this repo's per-topic folders (`01-networking/`, `02-windows-server/`, `03-active-directory/`, `04-clients-and-shares/`, `05-group-policy/`). This file is the condensed study reference only — never duplicate credentials or lab-specific sensitive values here.
