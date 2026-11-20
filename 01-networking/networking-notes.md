# Networking Notes

> Research notes and lab observations. Keep configurations and test traffic within an authorized lab.

## Research question

How should the enterprise lab network provide reliable reachability, name resolution, and isolation for `DC01`, `CLIENT01`, and future lab systems?

## Key takeaways

- `VMnet2` is a host-only network, so lab systems can communicate with the host and one another, but the setup must not rely on public or production network services.
- `192.168.50.0/24` is an isolated lab subnet; it should not be used if that range conflicts with an existing physical or virtual network.
- The domain controller is the authoritative DNS server for `technova.test`. `DC01` should normally be the preferred DNS server on domain members.
- Accurate IP addressing and DNS resolution are prerequisites for domain joins and Active Directory authentication.
- Host-only networking provides isolation, but it does not guarantee a firewall or system is secure. Only attach intended lab systems to `VMnet2`.

## Lab topology

```text
Host PC
└── VMware Workstation
    └── VMnet2 — Host-only — 192.168.50.0/24
        ├── DC01 / DNS / AD DS
        │   └── 192.168.50.10
        └── CLIENT01
            └── 192.168.50.20
```

This lab should not use a gateway unless internet or other upstream access is deliberately and safely provided. Domain join and Active Directory tests can work without internet access.

## Lab context

- **Date:** 2026-11-17
- **Hypervisor:** VMware Workstation
- **Virtual network:** `VMnet2`, host-only
- **DHCP:** Disabled for the lab subnet
- **Subnet:** `192.168.50.0/24`
- **Subnet mask:** `255.255.255.0`
- **Domain:** `technova.test`
- **NetBIOS domain:** `TECHNOVA`
- **Systems:** Windows Server 2012 R2 domain controller and Windows client

| System | Role | IPv4 address | Preferred DNS |
|---|---|---|---|
| `DC01` | Domain controller, DNS | `192.168.50.10` | `192.168.50.10` |
| `CLIENT01` | Domain member | `192.168.50.20` | `192.168.50.10` |

> Windows Server 2012 R2 is outside of support. Keep it isolated and do not use it for production workloads or sensitive data.

## Concepts to explore

| Concept | Purpose in this lab |
|---|---|
| Host-only network | Connects the lab VMs and host while avoiding a direct production-network dependency |
| IPv4 subnet | Defines the `192.168.50.0/24` address space and which addresses are on-link |
| Static addressing | Gives `DC01` and `CLIENT01` predictable addresses |
| DNS | Resolves `technova.test`, hostnames, and Active Directory service records |
| SRV records | Allow clients to discover domain services such as domain controllers and LDAP |
| Default gateway | Usually omitted in this isolated lab because all systems are on the same subnet |
| Windows Firewall | May block ICMP or AD/DNS traffic; compare firewall profiles before changing rules |
| DHCP vs. static IP | DHCP assignment is disabled so critical lab addresses do not change unexpectedly |

## VMware network setup

1. Open **VMware Workstation → Edit → Virtual Network Editor**.
2. Select **Change Settings** if administrator approval is required.
3. Create or select `VMnet2` and set it to **Host-only**.
4. Set the subnet to `192.168.50.0` and the subnet mask to `255.255.255.0`.
5. Disable the VMware DHCP service for this lab network.
6. Apply the changes.
7. Attach only `DC01` and the intended lab VMs to **Custom: VMnet2**.
8. Confirm `192.168.50.0/24` does not overlap a network that the host needs for normal work.

## Static IPv4 configuration

### DC01

| Setting | Value |
|---|---|
| IPv4 address | `192.168.50.10` |
| Subnet mask | `255.255.255.0` |
| Default gateway | Blank |
| Preferred DNS | `192.168.50.10` |

### CLIENT01

| Setting | Value |
|---|---|
| IPv4 address | `192.168.50.20` |
| Subnet mask | `255.255.255.0` |
| Default gateway | Blank |
| Preferred DNS | `192.168.50.10` |

Before joining the domain, the client must resolve `technova.test` and `DC01.technova.test` through `DC01`.

## Useful commands

Run these on `DC01`:

```cmd
hostname
ipconfig /all
Get-NetIPAddress
Get-DnsServerZone
Get-DnsServerResourceRecord -ZoneName technova.test
Get-Service DNS
Get-Service NTDS
```

Run these on `CLIENT01`:

```cmd
ipconfig /all
ping 192.168.50.10
nslookup technova.test
nslookup DC01.technova.test
nslookup -type=SRV _ldap._tcp.dc._msdcs.technova.test
tracert 192.168.50.10
```

For PowerShell DNS checks:

```powershell
Resolve-DnsName technova.test
Resolve-DnsName DC01.technova.test
Resolve-DnsName -Name _ldap._tcp.dc._msdcs.technova.test -Type SRV
Test-NetConnection 192.168.50.10 -Port 53
Test-NetConnection 192.168.50.10 -Port 389
```

Use `tracert` and `ping` as reachability diagnostics, not as proof that DNS or AD is healthy.

## Pre-domain verification sequence

1. Confirm `DC01` and `CLIENT01` show the VMnet2 adapter as connected.
2. Confirm the planned IPv4 addresses and `/24` subnet masks.
3. Confirm `CLIENT01` uses `192.168.50.10` as DNS.
4. Ping `DC01` from `CLIENT01`.
5. Resolve `technova.test` and `DC01.technova.test` through `DC01`.
6. Confirm the LDAP SRV record is visible.
7. Confirm required TCP ports can be reached before joining the domain.
8. Set accurate date, time, and time zone on both systems; Kerberos rejects excessive clock skew.

## Troubleshooting

| Symptom | Checks | Likely cause or next action |
|---|---|---|
| `ping` fails | `ipconfig /all`, adapter status, VMware network setting | Wrong virtual network, wrong subnet, disconnected adapter, or Windows Firewall |
| DNS returns public or unexpected answers | `nslookup` configuration, NIC DNS setting | The client is using a public or wrong DNS server |
| `nslookup` cannot find `technova.test` | DNS service, zone configuration, DC IP address | DNS service is stopped, zone is missing, or client cannot reach port 53 |
| Domain join cannot find the domain | SRV lookup, DNS suffix, connectivity to LDAP | DNS, SRV registration, or reachability issue |
| SRV lookup fails | Run `dcdiag`, verify DNS/AD services, restart/reconfigure DNS | Missing or stale service registration |
| Domain join succeeds but login fails | Time, credentials, DNS, domain controller health | Check the DC's logs and Kerberos/authentication prerequisites |
| Host or VM has unexpected internet reachability | VM adapter type and Virtual Network Editor | Recheck host-only isolation and VMware network configuration |

### DNS troubleshooting

```cmd
ipconfig /flushdns
ipconfig /registerdns
nslookup -type=SOA technova.test
nslookup -type=NS technova.test
nslookup -type=SRV _ldap._tcp.dc._msdcs.technova.test
```

On `DC01`, check DNS and domain-controller health:

```cmd
dcdiag /v
Get-Service DNS
Get-Service NTDS
Get-DnsServerZone
```

Only clear or re-register DNS records after identifying the underlying issue. Do not randomly restart DNS or remove records in a lab that is intended to demonstrate working behavior.

### Firewall troubleshooting

Begin with a connectivity test, then compare firewall profiles. If a lab-only rule is required, prefer limiting it to:

- the `192.168.50.0/24` subnet
- the specific required service
- the specific lab adapter
- the minimum required direction and scope

Do not disable Windows Firewall globally as a shortcut.

## Experiment log

| Date | Goal / change | Expected result | Actual result |
|---|---|---|---|
| 2026-11-17 | Configure `VMnet2` host-only network with `192.168.50.0/24` | Lab VMs can communicate without public DHCP | _Document result_ |
| 2026-11-17 | Assign static addresses to `DC01` and `CLIENT01` | Both VMs retain predictable addresses | _Document result_ |
| 2026-11-17 | Point `CLIENT01` DNS to `DC01` | Domain and SRV records resolve | _Document result_ |
| 2026-11-17 | Ping `DC01` from `CLIENT01` | Successful TCP/IP reachability | _Document result_ |
| 2026-11-17 | Resolve Active Directory DNS/SRV records | LDAP and domain-controller records visible | _Document result_ |

## Lessons learned

- Name resolution is essential to domain join; successful IP ping does not prove DNS or AD works.
- Static addresses prevent the domain controller and DNS server from changing unexpectedly.
- The default gateway should remain blank for this host-only lab unless a specific controlled route or upstream service is required.
- System time affects Kerberos authentication and should be checked with networking and DNS during troubleshooting.
- VMware host-only mode improves isolation, but it does not replace firewall and VM security controls.
- Confirm that lab addresses do not conflict with the host's existing networks before starting the experiment.

## Follow-up questions

- [ ] Does `nslookup` resolve `technova.test` and `DC01.technova.test` from `CLIENT01`?
- [ ] Does `_ldap._tcp.dc._msdcs.technova.test` return an SRV record for `DC01`?
- [ ] Can `CLIENT01` reach DNS port 53 and LDAP port 389 on `DC01`?
- [ ] What is the current default gateway on each VM?
- [ ] Is `VMnet2` isolated from the production network used for normal work?
- [ ] Did all network changes and tests remain inside `192.168.50.0/24`?

## References

- [Configure TCP/IP networking](https://learn.microsoft.com/en-us/windows-server/networking/technologies/network-subsystem/net-sub-interfaces-and-addresses) — Microsoft Learn — accessed 2026-11-17
- [DNS on Windows Server](https://learn.microsoft.com/en-us/windows-server/networking/dns/dns-top) — Microsoft Learn — accessed 2026-11-17
- [Active Directory and Active Directory Domain Services Port Requirements](https://learn.microsoft.com/en-us/troubleshoot/windows-server/networking/config-firewall-for-ad-domains-and-trusts) — Microsoft Learn — accessed 2026-11-17
- [Virtual Networking Concepts](https://docs.vmware.com/en/VMware-Workstation-Pro/) — VMware Docs — accessed 2026-11-17
