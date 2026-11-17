# Server Preparation — DC01 (Windows Server 2012 R2)

Lab: enterprise-infrastructure-lab
Domain (planned): `technova.test`
Hostname: `DC01`

## Environment

| Setting          | Value              |
|------------------|--------------------|
| OS               | Windows Server 2012 R2 Standard (Desktop Experience) |
| Hypervisor       | VMware Workstation |
| RAM              | 2 GB               |
| Processors       | 2 cores            |
| Hard Disk        | 60 GB              |
| Network Adapter  | Custom / VMnet2 (Host-only) |

> Windows Server 2012 R2 is past Extended Security Updates (ended Oct 13, 2026) — no more Microsoft security patches. Keep this VM isolated, lab-use only, never production.

## Step 1 — VM Settings

- Shut down the Server VM
- VMware Workstation → select VM → Edit virtual machine settings
- Confirm Memory / Processors / Hard Disk / Network Adapter match the table above

**Notes:** A VM (virtual machine) is a software-based computer running inside a physical computer.

## Step 2 — Isolated Network (VMnet2)

- Edit → Virtual Network Editor → Change Settings
- Select an unused VMnet (e.g. VMnet2) → set to **Host-only**
- Subnet IP: `192.168.50.0`
- Subnet mask: `255.255.255.0`
- Uncheck "Use local DHCP service to distribute IP addresses"
- Apply
- On the Server VM: Network Adapter → Custom → VMnet2

**Notes:**
- Host-only: private virtual network between host and lab VMs
- DHCP: automatically assigns IP addresses
- Subnet: logical division of an IP network

## Step 3 — Pre-flight Checks

Before touching AD config, confirm:
- Server boots and logs in as Administrator
- Desktop Experience is present
- VMware network adapter shows connected
- RAM allocation is correct (2 GB)
- Date, time, and time zone are accurate

**Notes:** A Domain Controller depends on accurate time — Kerberos authentication uses time-sensitive tickets.

## Step 4 — Rename the Server

- Server Manager → Local Server → click current computer name → Change
- New name: `DC01`
- Restart

**Naming convention for this lab:**
- `DC01` — Domain Controller 01
- `CLIENT01` — Windows Client 01
- `FS01` — File Server 01

**Notes:** Hostname identifies a computer on a network.

## Step 5 — Static IP Configuration

Control Panel → Network and Sharing Center → Change adapter settings → Ethernet → Properties → IPv4 → Properties

| Field            | Value             |
|------------------|-------------------|
| IP address       | 192.168.50.10     |
| Subnet mask      | 255.255.255.0     |
| Default gateway  | (leave blank)     |
| Preferred DNS    | 192.168.50.10     |

**Notes:** Static IP is manually assigned (not DHCP-leased) — servers use static IPs so other devices can reliably find them.

## Step 6 — Verify

```
hostname
```
Expected: `DC01`

```
ipconfig /all
```
Confirm Ethernet adapter shows the correct IP/DNS.

```
ping 192.168.50.10
```
Should get replies from itself.

## Step 7 — Snapshot (before AD install)

- VM menu → Snapshot → Take Snapshot
- Name: `DC01-Clean-Configured-Before-AD`

**Notes:** A snapshot records VM state to roll back to if configuration goes wrong.

## Step 8 — Install AD DS Role

- Server Manager → Manage → Add Roles and Features
- Role-based or feature-based installation → select DC01
- Server Roles → check **Active Directory Domain Services** → Add Features when prompted
- Next through remaining pages → Install

**Notes:** AD DS is the Windows Server role providing centralized directory services for a domain.

## Step 9 — Promote to Domain Controller

- Server Manager notification flag → Promote this server to a domain controller
- Select **Add a new forest**
- Root domain name: `technova.test`
- Select DNS Server + Global Catalog
- Set a strong DSRM password (store it safely)
- Leave default database/log/SYSVOL paths
- Run prerequisite check → Install
- Server restarts automatically

**Key terms:**

| Term           | Meaning                                       |
|----------------|------------------------------------------------|
| Forest         | Highest-level AD structure                     |
| Domain         | Administrative boundary for users/computers    |
| DNS            | Helps computers locate domain services         |
| Global Catalog | Helps locate objects throughout the forest     |
| DSRM           | Recovery mode for Active Directory             |

