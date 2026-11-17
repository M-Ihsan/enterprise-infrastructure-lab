# Domain Controller Setup ��� Promoting DC01

Promote `DC01` to the first domain controller in a new forest named `technova.test`, then verify the deployment. The AD DS role must already be installed; see [AD DS Installation](AD-DS-Installation.md).

## Lab details

- **Lab:** `enterprise-infrastructure-lab`
- **Server:** `DC01` — `192.168.50.10`
- **Domain:** `technova.test`
- **NetBIOS name:** `TECHNOVA`
- **Date completed:** 2026-11-17

## Promotion and configuration

1. In Server Manager, select the notification flag, then **Promote this server to a domain controller**.
2. Choose **Add a new forest** and enter `technova.test` as the root domain name.
3. Set the forest and domain functional levels to Windows Server 2012 R2. Select DNS Server and Global Catalog; leave RODC unchecked.
4. Set a strong DSRM password and store it securely outside this repository.
5. If the wizard warns that DNS delegation cannot be created, review the warning. In this isolated lab, this is expected when no parent DNS zone exists.
6. Confirm the suggested NetBIOS name is `TECHNOVA`.
7. Leave database, log, and SYSVOL paths at their defaults for this lab, review prerequisite results, and install if there are no blocking errors.
8. Allow the server to restart to complete promotion.

| Component | Default location |
|---|---|
| Database | `C:\Windows\NTDS` |
| Log files | `C:\Windows\NTDS` |
| SYSVOL | `C:\Windows\SYSVOL` |

## Verify the deployment

After restart, sign in with the domain Administrator account (for example, `TECHNOVA\Administrator`). Run these checks in an elevated PowerShell session:

```powershell
Get-ADDomain
Get-ADForest
dcdiag /v
Get-Service DNS
Get-Service NTDS
nslookup -type=SRV _ldap._tcp.dc._msdcs.technova.test
```

Review diagnostic output, including warnings or failures. An SRV lookup helps verify that DNS can locate domain controller services.

## Verification log

| Date | Check | Result |
|---|---|---|
| 2026-11-17 | Domain promotion completed | Success |
| 2026-11-17 | `Get-ADDomain` | Success — domain `technova.test`, NetBIOS `TECHNOVA`; DC01 holds PDC Emulator, RID Master, and Infrastructure Master roles. |
| 2026-11-17 | `dcdiag /v` | Two tests failed (`SystemLog`, `DFSREvent`). Core tests passed: Connectivity, Advertising, SysVolCheck, MachineAccount, NetLogons, Replications, RidManager, Services, VerifyReferences, and LocatorCheck. See troubleshooting note. |

## Troubleshooting note — 2026-11-17

`dcdiag` reported two failures associated with unexpected VM shutdown events:

- **SystemLog / DFSREvent:** The System event log showed two unexpected shutdown events. Around those reboot times, DFSR/SYSVOL reported a temporary domain-contact error (`Error 1355`). The error subsequently cleared, and `SysVolCheck` passed during the diagnostic run.
- **Assessment:** The timing is consistent with a transient issue around unclean VM shutdowns rather than evidence of a persistent AD configuration problem. Recheck if the failures recur.
- **Prevention:** Shut down `DC01` from within Windows (**Start → Shut down**) before closing VMware Workstation or shutting down the host. If DFSR/SYSVOL errors recur, review current event logs and rerun diagnostics.
- NtpClient and WinRM SPN warnings were observed and considered non-blocking in this single-DC lab. Reassess them if the lab topology changes or related functionality fails.

## Checklist

- [x] AD DS role installed.
- [x] New forest selected and `technova.test` configured as the root domain.
- [x] DNS and Global Catalog selected.
- [x] Promotion completed and server restarted.
- [x] `Get-ADDomain` returned the expected domain information.
- [x] Core `dcdiag` tests passed; the two reported failures are documented above.
- [ ] Shut down `DC01` normally and rerun `dcdiag /v` after a clean restart.

> Confirm domain controller health before creating users, groups, or OUs. Continue with [Users and Groups](Users-and-Groups.md).

## Security note

Never commit Administrator passwords, DSRM passwords, recovery keys, or other credentials to GitHub. A private repository is not a safe place to store secrets.

## References

- _Title — URL — date accessed_
