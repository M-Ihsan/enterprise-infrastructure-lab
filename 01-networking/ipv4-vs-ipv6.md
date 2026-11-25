# IPv4 vs IPv6 in This Lab

Why this lab uses IPv4, and what AD actually needs from either protocol.

## Lab details

- **Lab:** `enterprise-infrastructure-lab`
- **Date:** 2026-11-21

## Current lab addressing

| Machine | IPv4 |
|---|---|
| DC01 | `192.168.50.10` |
| CLIENT01 | `192.168.50.20` |
| Subnet | `192.168.50.0/24` |

Both machines use DC01 as their DNS server.

## IPv4 vs IPv6

| | IPv4 | IPv6 |
|---|---|---|
| Address size | 32-bit | 128-bit |
| Example | `192.168.50.10` | `2001:db8:50::10` |
| Address capacity | ~4.3 billion | 2¹²⁸ (enormously larger) |

### Why IPv4 is still used so widely

- Been around for decades, broad existing hardware/software support
- Private IPv4 ranges are convenient for internal networks
- Admins are already familiar with subnetting/configuration

### Why IPv6 matters

- IPv4 address space is limited
- Massive address space, less reliance on NAT
- Supports automatic address configuration
- Fits modern network architectures

## Does Active Directory need IPv4 specifically?

No. AD locates Domain Controllers and services through DNS, and DNS supports both:

| DNS record | Purpose |
|---|---|
| A | Maps a hostname to an IPv4 address |
| AAAA | Maps a hostname to an IPv6 address |
| SRV | Locates services such as LDAP and Kerberos |

A domain can run over a properly configured IPv6 network just as well as IPv4 — this lab uses IPv4 purely because it's simpler to set up, not because AD requires it.

**Important:** Don't disable IPv6 on Windows Server just because the lab currently uses IPv4 — some Windows components depend on IPv6 functioning internally even on an IPv4-addressed network.

## Key terms

| Term | Meaning |
|---|---|
| IPv4 | 32-bit addressing system |
| IPv6 | 128-bit addressing system |
| A record | DNS record mapping a hostname to an IPv4 address |
| AAAA record | DNS record mapping a hostname to an IPv6 address |
| Static IP | Manually assigned address |
| SLAAC | IPv6 Stateless Address Autoconfiguration |
| DHCPv6 | DHCP-based IPv6 configuration |

## Follow-up

- [ ] Build a separate IPv6 lab segment to compare both protocols practically

## References

- _Title — URL — date accessed_
