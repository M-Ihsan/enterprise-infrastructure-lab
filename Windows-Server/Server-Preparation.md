# Server Preparation — DC01 (Windows Server 2012 R2)

Prepare the Windows Server virtual machine for the `technova.test` Active Directory lab.

## Lab details

- **Lab:** `enterprise-infrastructure-lab`
- **Planned hostname:** `DC01`
- **Planned domain:** `technova.test`
- **Date:** _YYYY-MM-DD_

## Environment

| Setting | Value |
|---|---|
| Operating system | Windows Server 2012 R2 Standard (Desktop Experience) |
| Hypervisor | VMware Workstation |
| Memory | 2 GB |
| Processors | 2 cores |
| Virtual disk | 60 GB |
| Network adapter | Custom — VMnet2 (Host-only) |

> **Security note:** Windows Server 2012 R2 is out of support. Its Extended Security Updates ended October 13, 2026. Use this VM for isolated lab work only—not for production or sensitive data.

## 1. Configure the virtual machine

1. Shut down the server VM.
2. In VMware Workstation, select the VM and open **Edit virtual machine settings**.
3. Confirm the memory, processor, disk, and network adapter settings match the table above.

**Note:** A virtual machine (VM) is a software-based computer that runs on a physical host.

## 2. Configure the host-only network

1. Open **Edit → Virtual Network Editor** and select **Change Settings** if needed.
2. Select an unused VMnet, such as `VMnet2`, and set it to **Host-only**.
3. Set the subnet IP to `192.168.50.0` and subnet mask to `255.255.255.0`.
4. Disable **Use local DHCP service to distribute IP addresses** and apply the changes.
5. Set the server VM's network adapter to **Custom: VMnet2**.

A host-only network connects the host and VMs attached to that virtual network. Attach only intended lab systems, and confirm `192.168.50.0/24` does not conflict with another network you use.

## 3. Complete pre-flight checks

- [ ] The VM starts and you can sign in as Administrator.
- [ ] Desktop Experience is installed.
- [ ] The VMware network adapter is connected to `VMnet2`.
- [ ] Memory allocation is correct.
- [ ] Date, time, and time zone are accurate.

Accurate time is important because Kerberos authentication uses time-sensitive tickets.

## 4. Rename the server

1. Open **Server Manager → Local Server**.
2. Select the current computer name, then choose **Change**.
3. Set the computer name to `DC01` and restart when prompted.

Lab naming convention: `DC01` (domain controller), `CLIENT01` (Windows client), `FS01` (file server).

## 5. Configure a static IPv4 address

Open **Control Panel → Network and Sharing Center → Change adapter settings**. Right-click **Ethernet**, select **Properties**, then open **Internet Protocol Version 4 (TCP/IPv4)**.

| Setting | Value |
|---|---|
| IP address | `192.168.50.10` |
| Subnet mask | `255.255.255.0` |
| Default gateway | Leave blank |
| Preferred DNS server | `192.168.50.10` |

The DNS address is the planned address for this server's lab DNS configuration; verify DNS operation during domain setup.

## 6. Verify the basic configuration

Run these commands in Command Prompt:

```cmd
hostname
ipconfig /all
ping 192.168.50.10
```

Confirm the hostname is `DC01` and the Ethernet adapter shows the planned IP, subnet mask, and DNS setting. A ping to the server's own IP is only a basic local check; test connectivity to another intended lab VM when available.

## 7. Take a pre-configuration snapshot

In VMware Workstation, open **VM → Snapshot → Take Snapshot** and name it `DC01-Clean-Configured-Before-AD`.

A snapshot is a convenient rollback point, not a substitute for a backup. Reverting discards changes made after the snapshot.

## Preparation checklist

- [ ] VM settings are confirmed.
- [ ] Host-only network is configured.
- [ ] Server is renamed to `DC01`.
- [ ] Static IPv4 settings are recorded and verified.
- [ ] Pre-configuration snapshot is taken.

**Next:** Continue with [AD DS role installation](../Active-Directory/AD-DS-Installation.md) and [domain controller setup](../Active-Directory/Domain-Controller-Setup.md).
