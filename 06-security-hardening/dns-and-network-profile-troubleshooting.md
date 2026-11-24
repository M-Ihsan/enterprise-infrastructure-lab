# Troubleshooting: Network Profile Misclassification & DNS Root Hints

A real troubleshooting case from this lab: DC01's network adapter was misclassified as Public instead of DomainAuthenticated, and `dcdiag /test:dns` reported a DNS failure that turned out to be expected behavior, not a real problem.

## Lab details

- **Lab:** `enterprise-infrastructure-lab`
- **Host:** `DC01`
- **Date:** 2026-11-24

## Symptom 1 — Network category stuck on Public

`Get-NetConnectionProfile` showed:

```
Name             : Unidentified network
NetworkCategory  : Public
IPv4Connectivity : LocalNetwork
IPv6Connectivity : NoTraffic
```

Expected `DomainAuthenticated` for a domain controller's own interface.

### Diagnosis

- DNS, Netlogon, NlaSvc, NTDS all confirmed `Running`.
- SRV lookup for `_ldap._tcp.dc._msdcs.technova.test` already resolved correctly — AD itself was healthy.
- Root cause: this lab's `VMnet2` is host-only with **no default gateway**. Network Location Awareness (NLA) normally uses gateway reachability and other signals alongside domain authentication to classify a network; without a gateway, NLA can get stuck misidentifying the network even though domain authentication itself works fine.
- **Not evidence of a cause:** `IPv6Connectivity: NoTraffic` alone. An isolated host-only network can simply have no IPv6 traffic for unrelated reasons — don't treat it as proof of a root cause without more to go on.

### Fix

A plain `Restart-Service NlaSvc` failed (`Cannot stop service... it has dependent services`). Forcing it worked:

```powershell
Restart-Service -Name NlaSvc -Force
Get-NetConnectionProfile
```

Result:

```
Name             : technova.test
NetworkCategory  : DomainAuthenticated
```

**Status: Resolved.**

## Symptom 2 — `dcdiag /test:dns /v` reports a DNS failure

Summary showed:

```
Forw (Forwarders/Root hints) on DC01: FAIL
technova.test failed test DNS
Error: Both root hints and forwarders are not configured or broken.
```

All 13 root servers listed as `[Invalid (unreachable)]`, each reporting a PTR timeout for `1.0.0.127.in-addr.arpa`.

### Diagnosis

- Root hints point to **external internet DNS root servers**. This lab's host-only VMnet has no gateway/internet path, so these servers are genuinely unreachable — the failure is expected, not a misconfiguration.
- The PTR timeout for `1.0.0.127.in-addr.arpa` (localhost reverse lookup) ties to the same lack-of-internet cause, not a broken internal reverse zone.

**Decision: do not add DNS forwarders or change network config just to make this specific test pass.** Root hints/forwarders are for resolving external names — irrelevant to this isolated lab's actual purpose.

### What actually matters — internal AD DNS — verified separately

| Check | Command | Result |
|---|---|---|
| DNS service | `Get-Service DNS` | Running |
| DC hostname (A/AAAA) | `Resolve-DnsName dc01.technova.test -Server 192.168.50.10` | A → `192.168.50.10`; AAAA → `fe80::e96a:a486:b5cd:bc6a` (link-local, not routable — expected) |
| LDAP SRV | `Resolve-DnsName _ldap._tcp.dc._msdcs.technova.test -Type SRV -Server 192.168.50.10` | Target `dc01.technova.test`, port 389 — correct |
| Kerberos SRV | `Resolve-DnsName _kerberos._tcp.dc._msdcs.technova.test -Type SRV -Server 192.168.50.10` | Target `dc01.technova.test`, port 88 — correct |

**Status: Internal AD DNS fully healthy.** External DNS resolution is a separate, lab-irrelevant requirement.

## Key distinction for the notebook

| DNS component | Purpose |
|---|---|
| Internal DNS records (A/AAAA) | Domain computers locate DCs and services |
| SRV records | Clients locate LDAP, Kerberos, and other AD services |
| Root hints | DNS server finds external internet DNS information |
| Forwarders | Sends external queries to another DNS server |
| PTR records | Maps an IP address back to a hostname |

Priority for an AD lab: internal DNS must work. Internet name resolution is unrelated and expected to fail in an isolated network.

## Lessons learned

- A `dcdiag` test failure isn't automatically a real problem — read what the specific test actually checks before reacting to it.
- NLA network classification and actual AD functionality are separate concerns; a "Public" label didn't mean AD was broken.
- `Restart-Service` sometimes needs `-Force` when a service has dependents — the plain command fails safely rather than silently doing nothing.
- Link-local IPv6 addresses (`fe80::`) are normal and not something to "fix."

## References

- _Title — URL — date accessed_
