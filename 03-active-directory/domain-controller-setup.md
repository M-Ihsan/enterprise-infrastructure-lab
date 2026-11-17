# Domain Controller Setup

> Planning and validation notes for a domain controller in an authorized lab.

## Goal and design

- **Date:** _YYYY-MM-DD_
- **Lab domain:** _Use a reserved lab name; avoid real organizational details_
- **Server / version:** _Add details_
- **DNS plan:** _Describe the intended lab setup_
- **Recovery plan:** _Note snapshots or other recovery steps_

## Design notes

_Summarize relevant concepts, assumptions, and design choices. Add references._

## Verification log

| Date | Check | Result |
|---|---|---|
| 2026-11-17 | Domain promotion completed | Success |
| 2026-11-17 | `Get-ADDomain` | Success — domain `technova.test`, NetBIOS `TECHNOVA`, DC01 holds PDC Emulator, RID Master, and Infrastructure Master roles |
| 2026-11-17 | `dcdiag /v` | 2 of approximately 18 tests failed (`SystemLog`, `DFSREvent`) — see troubleshooting note below. Core tests passed: Connectivity, Advertising, SysVolCheck, MachineAccount, NetLogons, Replications, RidManager, Services, VerifyReferences, and LocatorCheck. |

## Validation checklist

- [x] Confirmed the expected domain and directory services are available.
- [x] Checked DNS behavior within the lab.
- [x] Confirmed basic network connectivity and time configuration.
- [x] Reviewed diagnostic warnings and failures; see troubleshooting note below.

## Troubleshooting note — 2026-11-17

`dcdiag` reported two failures, both associated with unexpected VM shutdowns:

- **SystemLog / DFSREvent:** The System event log showed two unexpected shutdown events (the VM crashed or was closed uncleanly). Around those reboots, DFSR/SYSVOL reported a temporary domain-contact error (`Error 1355`). The error subsequently cleared; `SysVolCheck` passed, indicating SYSVOL was healthy at the time of this check.
- **Assessment:** `Get-ADDomain` returned the expected domain information, and the listed core connectivity, replication, and role-holder tests passed. The observed failures appear related to the unclean shutdowns rather than a persistent AD configuration issue.
- **Prevention:** Shut down `DC01` from within Windows (**Start → Shut down**) before closing VMware Workstation or shutting down the host. If the DFSR/SYSVOL errors recur, review current event logs and rerun diagnostics rather than assuming they have cleared.
- NtpClient and WinRM SPN warnings were observed and considered non-blocking in this single-DC lab. Reassess them if the lab topology changes or related functionality fails.

## Follow-up

- [ ] Use a normal Windows shutdown for `DC01`.
- [ ] Rerun `dcdiag /v` after a clean restart and record the results.

## References

- _Title — URL — date accessed_
