# Department File Shares — FS01 (on DC01)

Configure SMB shares and NTFS permissions so each department can only access its own folder.

## Lab details

- **Lab:** `enterprise-infrastructure-lab`
- **Host:** `DC01`
- **Date:** 2026-11-19

## Folder structure

```
C:\CompanyData
│
├── HR
├── IT
├── Finance
└── Management
```

## Procedure (per department)

1. Create `C:\CompanyData` and one subfolder per department.
2. Server Manager → **File and Storage Services** → **Shares** → **Tasks** → **New Share**.
3. Select **SMB Share – Quick**, choose the department folder.
4. Name the share after the department (e.g. `HR`).
5. On the Permissions step, select **Customize permissions**.
6. Disable inheritance and convert inherited permissions into explicit permissions.
7. Remove both `BUILTIN\Users` entries.
8. Retain **SYSTEM** and **Administrators** with Full Control. **CREATOR OWNER** remains as a default.
9. Add the department's global group with **Modify** (applies to this folder, subfolders and files).
10. Confirm the settings and click **Create**.
11. Repeat for each department.

## Permissions

| Resource | Authorized group | Access level |
|---|---|---|
| HR folder | GG_HR | Modify |
| IT folder | GG_IT | Modify |
| Finance folder | GG_Finance | Modify |
| Management folder | GG_Management | Modify |

### NTFS permission entries used

| Entry | Purpose |
|---|---|
| SYSTEM | Full Control — Windows system processes |
| Administrators | Full Control — administrative access |
| CREATOR OWNER | Default ACL behavior for objects created within the folder |
| GG_HR / GG_IT / GG_Finance / GG_Management | Modify — department-specific access only |

`BUILTIN\Users` is explicitly removed from every share's NTFS permissions — it is not part of the intended design.

## Key terms

| Term | Meaning |
|---|---|
| SMB | Protocol used for network file sharing |
| NTFS permissions | Control access to files/folders on an NTFS volume |
| Share permissions | Control access through the network share |
| Least privilege | Give users only the access they need |

**Note:** Share permissions and NTFS permissions are evaluated together, not one simply "winning" over the other — effective access depends on how allow/deny entries and scope combine across both layers. Don't assume NTFS permissions alone determine what a user can actually do over the network; verify access from a domain-joined client (see `client-join-and-testing.md`).

## Verification log

| Date | Check | Result |
|---|---|---|
| 2026-11-19 | HR share created, inheritance disabled, BUILTIN\Users removed, GG_HR has Modify | _Result_ |
| 2026-11-19 | IT share created, inheritance disabled, BUILTIN\Users removed, GG_IT has Modify | _Result_ |
| 2026-11-19 | Finance share created, inheritance disabled, BUILTIN\Users removed, GG_Finance has Modify | _Result_ |
| 2026-11-19 | Management share created, inheritance disabled, BUILTIN\Users removed, GG_Management has Modify | _Result_ |

## Security note

Lab-only test data. No real passwords, personal information, or sensitive files are stored in `C:\CompanyData`.

## References

- _Title — URL — date accessed_
