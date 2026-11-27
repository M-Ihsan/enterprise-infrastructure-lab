# Endpoint Security — Defender Assessment

Introduce endpoint security concepts and assess Windows Defender's actual health on `CLIENT01` — not just whether it's enabled, but whether its threat intelligence is current.

## Lab details

- **Lab:** `enterprise-infrastructure-lab`
- **Target:** `CLIENT01`
- **Date:** 2026-11-26

## Roadmap position

| Area | Status |
|---|---|
| Networking foundations | Studied and revised |
| Windows Server fundamentals | Completed initial setup |
| AD DS, domain, DNS | Configured |
| Users, groups, OUs | Configured |
| Domain-joined client | Completed |
| Shared folders, SMB & NTFS permissions | Practiced |
| Group Policy (GPO) | Practiced |
| Domain password policy | Configured and understood |
| Fine-Grained Password Policies (FGPP) | Practiced |
| Firewall, network profiles & DNS diagnostics | Initial assessment |
| **Endpoint security** | **This chapter** |
| Security auditing & Event Viewer | Upcoming |
| Privileged access & AD security | Upcoming |
| Troubleshooting scenarios | Upcoming |

## 1. What is an endpoint?

A device that connects to and is managed within a network — desktops, laptops, servers, workstations, mobile devices.

| Machine | Role | Endpoint? |
|---|---|---|
| DC01 | Domain Controller | Yes — high-value (holds identity infrastructure) |
| CLIENT01 | Windows workstation | Yes |
| Physical laptop | VMware host | Yes |

## 2. What is endpoint security?

Protecting individual devices against threats, unauthorized access, malicious software, unsafe configurations, and suspicious activity. Purpose: detect suspicious activity → prevent malicious software from running → block/isolate threats → record what happened → give an admin enough information to investigate.

## 3. Core components

| Component | Purpose |
|---|---|
| Antivirus (AV) | Detects, blocks, removes known/suspicious malicious software |
| Real-time protection | Monitors files/activity as they're accessed or executed |
| EDR (Endpoint Detection and Response) | Monitors endpoint activity for suspicious behavior; supports investigation and incident response |
| Security updates | Fix exploitable vulnerabilities in OS/software |

**Distinction:** AV and EDR are not the same thing — AV mainly blocks known malicious software, EDR provides broader monitoring/investigation/response. Modern endpoint protection products often combine both.

## Practical 1 — Assess Windows Defender on CLIENT01

Chosen deliberately as the target (not the DC) to avoid changing anything on the Domain Controller for this first pass.

### Objective

Determine: is antivirus installed and enabled? Is real-time protection on? Is it actually functioning? When were threat-intelligence (signature) updates last applied?

### Steps

1. **Windows Security GUI** (CLIENT01): Start → search "Windows Security" → **Virus & threat protection**. Inspect only — no changes.
2. **PowerShell:**

```powershell
Get-MpComputerStatus
```

**Command breakdown:** `Get` = retrieve info, `Mp` = Microsoft Defender (Malware Protection), `ComputerStatus` = this computer's protection status. Translation: "give me this computer's current Defender security status."

### Key properties to read

| Property | What it tells you |
|---|---|
| `AMServiceEnabled` | Is the antimalware service enabled? |
| `AntivirusEnabled` | Is antivirus protection enabled? |
| `RealTimeProtectionEnabled` | Is real-time protection enabled? |
| `AntivirusSignatureVersion` | Which signature (threat intelligence) version is installed? |
| `AntivirusSignatureLastUpdated` | When were signatures last updated? |
| `QuickScanAge` | Days since last quick scan |
| `FullScanAge` | Days since last full scan |

## Results — 2026-11-26

### Protection status: enabled

| Property | Result | Interpretation |
|---|---|---|
| `AMServiceEnabled` | True | Defender service enabled |
| `AntivirusEnabled` | True | Antivirus enabled |
| `AntispywareEnabled` | True | Antispyware enabled |
| `RealTimeProtectionEnabled` | True | Real-time protection enabled |
| `BehaviorMonitorEnabled` | True | Behavior monitoring enabled |
| `OnAccessProtectionEnabled` | True | File-access protection enabled |
| `IoavProtectionEnabled` | True | Download/attachment inspection enabled |
| `NISEnabled` | True | Network inspection enabled |
| `ComputerState` | 0 | No unhealthy state flags reported |

### Finding — signature intelligence is severely outdated

```
AntivirusSignatureAge         : 2620
AntivirusSignatureLastUpdated : 9/23/2019 10:12:58 PM
AntivirusSignatureVersion     : 1.303.25.0
```

**What a signature is:** Defender's threat-intelligence database, used to recognize known malicious software. Protection can be fully "enabled" and still be ineffective against newer threats if this intelligence is years out of date.

**Last update: September 23, 2019** — roughly 2,620 days old at assessment time. Likely explained by the isolated lab network having no access to Windows Update sources, combined with this being an older, unsupported Windows 10 install not suited for real-world protection.

**Key lesson: protection enabled ≠ protection up to date.** Always check both.

### Scan history

```
FullScanAge       : 4294967295
FullScanEndTime   : (empty)
FullScanStartTime : (empty)

QuickScanAge       : 6
QuickScanEndTime   : 11/19/2026 10:20:11 PM
QuickScanStartTime : 11/19/2026 10:03:06 PM
```

- `4294967295` = the max value of a 32-bit unsigned integer — a sentinel meaning **no recorded full scan / value unavailable**, not a literal day count. Confirmed by the empty start/end timestamps alongside it.
- Quick scan: last recorded ~6 days before this assessment.
- **Quick scan** = checks common threat locations. **Full scan** = broader scan of files/locations. A full scan isn't something to run constantly just to populate this field.

### Other findings

| Property | Value | Meaning |
|---|---|---|
| `IsVirtualMachine` | True | Defender correctly recognizes this as a VM |
| `IsTamperProtected` | False | Tamper Protection not reported as enabled |
| `LastQuickScanSource` | 2 | Numeric scan-source code — not interpreted further without confirming the version-specific mapping |

## Security lesson

An antivirus showing "enabled" in every status field is not the same as an antivirus that's actually effective — signature/threat-intelligence freshness matters just as much as the enabled/disabled flags, and has to be checked separately.

## Verification log

| Date | Check | Result |
|---|---|---|
| 2026-11-26 | `Get-MpComputerStatus` run on CLIENT01 | Success — all protection flags True, `ComputerState = 0` |
| 2026-11-26 | Signature freshness reviewed | Outdated — last updated 2019-09-23 (~2620 days) |
| 2026-11-26 | Scan history reviewed | Quick scan ~6 days old; no recorded full scan (`FullScanAge` sentinel value) |

## Not yet covered

- How Windows actually receives/applies security intelligence updates, and why update management matters (next session)
- Security auditing & Event Viewer
- Privileged access & AD security
- Endpoint security policy configuration
- Tamper Protection investigation

## References

- _Title — URL — date accessed_
