# Windows Security Hardening — Firewall (Part A/B) & Defender Note (Part C)

Inspect and verify Windows Firewall configuration on `DC01`, understand inbound/outbound rules, and note a compatibility limitation around Windows Defender on this OS.

## Lab details

- **Lab:** `enterprise-infrastructure-lab`
- **Host:** `DC01`
- **Date:** _YYYY-MM-DD_

> Windows Server 2012 R2 is an old, unsupported platform. Keep it isolated in the host-only lab network (`VMnet2`) — these exercises are for learning, not production deployment.

## Part A — Windows Firewall

### 1. Basics

Windows Firewall controls network traffic entering and leaving a computer.

- **Inbound:** traffic coming into the server (e.g. `CLIENT01` accessing an SMB share on `DC01` is inbound traffic to `DC01`)
- **Outbound:** traffic leaving the server

Rules decide which traffic is allowed or blocked.

### 2. Open Windows Firewall

```
Win + R → wf.msc
```

Opens **Windows Firewall with Advanced Security**. Left panel shows: Inbound Rules, Outbound Rules, Connection Security Rules, Monitoring.

### 3. Inspect firewall profiles

Middle panel → **Windows Firewall Properties**.

| Profile | Purpose |
|---|---|
| Domain | When connected to an AD domain network |
| Private | Trusted private networks |
| Public | Untrusted public networks |

For this lab, the **Domain** profile matters most.

### Recommended lab values (per profile)

| Setting | Recommended lab value |
|---|---|
| Firewall state | On |
| Inbound connections | Block (default) |
| Outbound connections | Allow (default) |

### 4. Configure the profiles

1. Domain Profile tab → Firewall state: **On**.
2. Inbound connections: **Block (default)**.
3. Outbound connections: **Allow (default)**.
4. Repeat for Private and Public profiles.
5. Apply → OK.

**Important:** don't disable the firewall to solve connectivity problems, and don't randomly disable existing inbound rules — the DC needs its rules for DNS, AD DS, and file sharing to keep working.

### 5. Verify via PowerShell

```powershell
Get-NetFirewallProfile | Format-Table Name, Enabled, DefaultInboundAction, DefaultOutboundAction
```

Expected: `Enabled = True`, `DefaultInboundAction = Block`, `DefaultOutboundAction = Allow` for all profiles.

```powershell
Get-NetConnectionProfile
```

Shows the currently active network profile.

## Part B — Independent practical: inspect existing rules

In `wf.msc`:

1. Open **Inbound Rules** → note Name/Enabled columns, identify a few enabled rules.
2. Open **Outbound Rules** → inspect a few enabled rules.

**Do not create or delete any rules** — this exercise is about understanding what's already protecting the server.

### Concepts check

1. **Difference between inbound and outbound rules?** — _your answer_
2. **Why block unsolicited inbound connections by default?** — _your answer_
3. **Why shouldn't DC firewall rules be randomly disabled?** — _your answer_
4. **Purpose of the Domain profile?** — _your answer_

## Part C — Note on Windows Defender

**Windows Server 2012 R2 does not ship with Microsoft Defender Antivirus built in and active by default** the way modern Windows Server versions do. Supported Defender Antivirus deployment on this OS requires onboarding through Microsoft Defender for Endpoint's modern unified solution — not something this lab's standalone 2012 R2 box provides out of the box.

**Decision:** skip trying to install a modern Defender feature this OS doesn't support by default. Defender concepts will be covered later using appropriate modern Windows documentation/lab environment instead.

## Verification log

| Date | Check | Result |
|---|---|---|
| 2026-11-24 | `Get-NetFirewallProfile` | Enabled=True all 3 profiles; action columns `NotConfigured` = using OS built-in default (Block inbound/Allow outbound), confirmed by GUI overview |
| 2026-11-24 | `Get-NetConnectionProfile` | Resolved — see `dns-and-network-profile-troubleshooting.md` (was Public, now `DomainAuthenticated`) |
| 2026-11-24 | Enabled inbound rules (ActiveStore) | 68 rules, all `Action = Allow` — expected set for AD DS/DNS role, no explicit Block rules active |
| 2026-11-24 | Enabled outbound rules (ActiveStore) | 36 rules, all `Action = Allow` — consistent with default-allow outbound |
| 2026-11-24 | Inbound/outbound rules inspected, no changes made | Success |

## Troubleshooting note — 2026-11-24 (resolved)

Root cause confirmed and fixed. `Restart-Service NlaSvc -Force` corrected the network category from `Public`/"Unidentified network" to `DomainAuthenticated`/`technova.test`. Full investigation, including the DNS root-hints red herring, is documented in `dns-and-network-profile-troubleshooting.md`.

**Status:** Resolved. `NetworkCategory = DomainAuthenticated` confirmed.

**Fix to try:**
```powershell
Restart-Service NlaSvc
Get-NetConnectionProfile
```
If it still shows Public after restart, a full reboot of DC01 usually forces NLA to re-evaluate. Re-check that `NetworkCategory` becomes `DomainAuthenticated` once DC01 can confirm it's on its own domain network.

## Key terms

| Term | Meaning |
|---|---|
| Inbound rule | Controls traffic coming into the computer |
| Outbound rule | Controls traffic leaving the computer |
| Domain profile | Firewall profile active when connected to an AD domain network |
| Private profile | Firewall profile for trusted private networks |
| Public profile | Firewall profile for untrusted public networks |

## Lessons learned

- _What did you learn about default inbound/outbound firewall posture and why DC rules need care?_

## Next

Continue Windows security hardening — remaining topics (security baselines, LAPS) once covered.

## References

- _Title — URL — date accessed_
