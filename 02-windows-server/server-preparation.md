# Server Preparation — DC01

Prepare the Windows Server virtual machine for a future Active Directory lab.

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

> **Security note:** Windows Server 2012 R2 is out of support, and its Extended Security Updates ended on October 13, 2026. Use this VM for isolated lab work only—not for production or sensitive data.

## 1. Configure the virtual machine

1. Shut down the server VM.
2. In VMware Workstation, select the VM and open **Edit virtual machine settings**.
3. Confirm the memory, processor, disk, and network adapter settings match the table above.

**Note:** A virtual machine (VM) is a software-based computer that runs on a physical host.

## 2. Configure the host-only network

1. Open **Edit → Virtual Network Editor** and select **Change Settings** if needed.
2. Select an unused VMnet, such as `VMnet2`, and set it to **Host-only**.
3. Configure the subnet:
   - **Subnet IP:** `192.168.50.0`
   - **Subnet mask:** `255.255.255.0`
4. Disable **Use local DHCP service to distribute IP addresses**.
5. Apply the changes.
6. Set the server VM's network adapter to **Custom: VMnet2**.

**Network notes:**

- A host-only network connects the host and VMs attached to that virtual network. It is not automatically isolated from the host or other VMs on the same VMnet.
- Attach only intended lab systems to this network.
- Confirm `192.168.50.0/24` does not conflict with another network you use.
- **DHCP** automatically assigns IP addresses; this lab uses a manually assigned (static) address instead.
- **Subnet:** a logical division of an IP network.

## 3. Complete pre-flight checks

Before configuring the server, confirm that:

- [ ] The VM starts and you can sign in as Administrator.
- [ ] Desktop Experience is installed.
- [ ] The VMware network adapter is connected to `VMnet2`.
- [ ] The VM has the planned memory allocation.
- [ ] The date, time, and time zone are correct.

**Note:** Accurate time is important for domain environments because Kerberos authentication uses time-sensitive tickets.

## 4. Rename the server

1. Open **Server Manager → Local Server**.
2. Select the current computer name, then choose **Change**.
3. Set the computer name to `DC01`.
4. Restart the server when prompted.

**Naming convention for this lab:**

- `DC01` — domain controller
- `CLIENT01` — Windows client
- `FS01` — file server

**Note:** A hostname identifies a computer on a network.

## 5. Configure a static IPv4 address

Open **Control Panel → Network and Sharing Center → Change adapter settings**. Right-click **Ethernet**, select **Properties**, then open **Internet Protocol Version 4 (TCP/IPv4)**.

| Setting | Value |
|---|---|
| IP address | `192.168.50.10` |
| Subnet mask | `255.255.255.0` |
| Default gateway | Leave blank |
| Preferred DNS server | `192.168.50.10` |

**Note:** A static IP is manually assigned rather than DHCP-leased. A static address makes it easier for other lab systems to locate the server. The DNS value is the planned address for this server's lab DNS configuration; verify DNS operation as part of the later domain setup.

## 6. Verify the basic configuration

Run the following commands in Command Prompt.

Check the hostname:

```cmd
hostname
```

Expected output:

```text
DC01
```

Review the network configuration:

```cmd
ipconfig /all
```

Confirm that the Ethernet adapter shows the planned IP address, subnet mask, and DNS setting.

You can also check that the server responds on its own IP address:

```cmd
ping 192.168.50.10
```

A successful ping to its own address is a basic local check; it does not confirm connectivity to another VM. If another intended lab machine is attached to `VMnet2`, test connectivity between the two machines as well.

## 7. Take a pre-configuration snapshot

1. In VMware Workstation, open **VM → Snapshot → Take Snapshot**.
2. Name the snapshot `DC01-Clean-Configured-Before-AD`.

A snapshot provides a convenient rollback point for this lab VM. It is not a substitute for a backup, and reverting to it will discard changes made after it was taken.

## Preparation summary

- [ ] VM settings are confirmed.
- [ ] Host-only network is configured.
- [ ] Server is renamed to `DC01`.
- [ ] Static IPv4 settings are recorded and verified.
- [ ] Pre-configuration snapshot is taken.

**Next:** Document the Active Directory Domain Services installation and domain controller setup in their respective sections.

## Key terms

| Term | Meaning |
|---|---|
| Virtual machine (VM) | Software-based computer running on a physical host |
| Host-only network | Private virtual network between host and VMs on the same VMnet |
| DHCP | Automatically assigns IP addresses |
| Static IP | Manually assigned IP address |
| Subnet | Logical division of an IP network |
| Kerberos | AD's authentication protocol — relies on accurate system time |
| Snapshot | A rollback point for a VM's current state, not a backup |

## References

- _Title — URL — date accessed_
