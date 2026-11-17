# Domain Controller Setup — Promoting DC01

Promote `DC01` to the first domain controller in a new forest named `technova.test`, then verify the deployment. The AD DS role must already be installed; see [`ad-ds-installation.md`](ad-ds-installation.md).

## Lab details

- **Lab:** `enterprise-infrastructure-lab`
- **Server:** `DC01` — `192.168.50.10`
- **Domain:** `technova.test`
- **NetBIOS name:** `TECHNOVA`
- **Date completed:** 2026-11-17

## 1. Start promotion

1. In Server Manager, select the notification flag.
2. Select **Promote this server to a domain controller**.

The Active Directory Domain Services Configuration Wizard opens.

## 2. Configure deployment

1. Select **Add a new forest**.
2. Enter `technova.test` as the root domain name.
3. Select **Next**.

**Key terms:**

- **Forest:** the top-level structure of an Active Directory environment.
- **Root domain:** the first domain created in a new forest.

## 3. Configure domain controller options

| Setting | Selection |
|---|---|
| Forest functional level | Windows Server 2012 R2 |
| Domain functional level | Windows Server 2012 R2 |
| DNS Server | Checked |
| Global Catalog | Checked |
| Read-only domain controller (RODC) | Unchecked |

Set a strong DSRM password and store it securely outside this repository.

**Key terms:**

- **Functional level:** determines the Active Directory features available to the forest or domain.
- **Global Catalog (GC):** helps locate directory objects across the forest.
- **RODC:** a read-only domain controller, often used in branch-office scenarios.
- **DSRM:** Directory Services Restore Mode, used for directory recovery.

## 4. Review DNS options

If the wizard reports that a DNS delegation cannot be created, review the warning. In this isolated lab, that warning is expected when there is no parent DNS zone to delegate from. Select **Next** if the warning is understood and there are no blocking errors.

**Note:** DNS delegation directs queries for a domain to the DNS server responsible for that domain.

## 5. Confirm the NetBIOS name

The wizard should suggest `TECHNOVA`. Confirm the suggested name and select **Next**.

**Note:** The NetBIOS name is a shorter, legacy-compatible name for the domain.

## 6. Review database and SYSVOL paths

Leave the default paths for this lab unless you have a specific reason to change them.

| Component | Default location |
|---|---|
| Database | `C:\Windows\NTDS` |
| Log files | `C:\Windows\NTDS` |
| SYSVOL | `C:\Windows\SYSVOL` |

**Note:** SYSVOL stores domain-wide files, including Group Policy data and logon scripts.

## 7. Run prerequisite checks and install

1. Review the configuration summary and select **Next**.
2. Wait for the prerequisite check and review its warnings.
3. If there are no blocking errors, select **Install**.
4. Allow the server to restart to complete promotion.

## 8. Verify the deployment

After restart, sign in using the domain Administrator account. The sign-in context should be similar to:

```text
TECHNOVA\Administrator
```

Run these checks in an elevated PowerShell session:

```powershell
Get-ADDomain
Get-ADForest
 dcdiag
Get-Service DNS
Get-Service NTDS
nslookup -type=SRV _ldap._tcp.dc._msdcs.technova.test
```

`dcdiag` may report warnings or failures that need investigation; review the output rather than treating every warning as a blocker. An SRV lookup helps verify that DNS can locate domain controller services.

## Verification log

| Date | Check | Result |
|---|---|---|
| 2026-11-17 | Domain promotion completed | Success |
| 2026-11-17 | `Get-ADDomain` | Returned domain `technova.test` and NetBIOS name `TECHNOVA`; `DC01` held the PDC Emulator, RID Master, and Infrastructure Master roles. |
| 2026-11-17 | `dcdiag /v` | Two tests failed (`SystemLog` and `DFSREvent`). Core tests passed, including Connectivity, Advertising, SysVolCheck, MachineAccount, NetLogons, Replications, RidManager, Services, VerifyReferences, and LocatorCheck. See troubleshooting note. |

## Troubleshooting note — 2026-11-17

The two `dcdiag` failures were associated with unexpected VM shutdown events:

- **SystemLog and DFSREvent:** The System event log showed two unexpected shutdown events. Around those reboot times, DFSR/SYSVOL reported a temporary domain-contact error (`Error 1355`). The error subsequently cleared, and `SysVolCheck` passed at the time of the diagnostic run.
- **Assessment:** The results are consistent with a transient issue around unclean VM shutdowns, rather than evidence of a persistent AD configuration problem. This is an assessment based on the observed logs and passing tests; recheck if the failures recur.
- **Prevention:** Shut down `DC01` from within Windows (**Start → Shut down**) before closing VMware Workstation or shutting down the host. If DFSR/SYSVOL errors recur, review current event logs and rerun diagnostics.
- NtpClient and WinRM SPN warnings were observed and considered non-blocking in this single-DC lab. Reassess them if the lab topology changes or related functionality fails.

## Checklist

- [x] AD DS role installed.
- [x] A new forest was selected and `technova.test` configured as the root domain.
- [x] DNS and Global Catalog were selected.
- [x] Promotion completed and the server restarted.
- [x] `Get-ADDomain` returned the expected domain information.
- [x] Core `dcdiag` tests passed; the two reported failures are documented above.
- [ ] Shut down `DC01` normally and rerun `dcdiag /v` after a clean restart.

> Confirm domain controller health before creating users, groups, or OUs. Those topics belong in [`users-and-groups.md`](users-and-groups.md).

## Key terms

| Term | Meaning |
|---|---|
| Forest | Top-level structure of an Active Directory environment |
| Root domain | First domain created in a new forest |
| Global Catalog | Helps locate directory objects across the forest |
| RODC | Read-only domain controller |
| DSRM | Directory Services Restore Mode for directory recovery |
| SYSVOL | Shared folder for domain policies and scripts |
| NetBIOS name | Short, legacy-compatible domain name |

## Security note

Never commit Administrator passwords, DSRM passwords, recovery keys, or other credentials to GitHub. A private repository is not a safe place to store secrets. Keep credentials in a secure location outside the repository.

## References

- _Title — URL — date accessed_
