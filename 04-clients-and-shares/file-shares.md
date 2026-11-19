# Department File Shares — FS01 (on DC01)

Configure SMB shares and NTFS permissions so each department can only access its own folder.

## Lab details

- **Lab:** `enterprise-infrastructure-lab`
- **Host:** `DC01`
- **Date:** _YYYY-MM-DD_

## Folder structure

```
C:\CompanyData
│
├── HR
├── IT
├── Finance
└── Management
```

## Steps

1. Create `C:\CompanyData` and the four department subfolders.
2. Server Manager → **File and Storage Services** → **Shares** → **Tasks** → **New Share**.
3. Choose **SMB Share – Quick**.
4. Select the department folder, name the share after the department (e.g. `HR`).
5. Configure permissions (see table below).
6. Repeat for each department.

## Permissions

| Resource | Authorized group | Access level |
|---|---|---|
| HR folder | GG_HR | Modify |
| IT folder | GG_IT | Modify |
| Finance folder | GG_Finance | Modify |
| Management folder | GG_Management | Modify |

Administrators and SYSTEM keep Full Control on all shares.

### NTFS permission entries used

| Entry | Purpose |
|---|---|
| SYSTEM | Windows system processes |
| Administrators | Administrative control |
| CREATOR OWNER | Controls permissions for objects created by their owner |
| GG_HR / GG_IT / etc. | Department-specific access |
| BUILTIN\Users | Broad access for local users |

## Key terms

| Term | Meaning |
|---|---|
| SMB | Protocol used for network file sharing |
| NTFS permissions | Control access to files/folders on an NTFS volume |
| Share permissions | Control access through the network share |
| Least privilege | Give users only the access they need |

**Note:** Share permissions and NTFS permissions are separate and both apply — the more restrictive effective permission wins. Correct-looking NTFS permissions don't guarantee the share is actually restricted; verify from a domain-joined client (see `client-join-and-testing.md`).

## Verification log

| Date | Check | Result |
|---|---|---|
| _YYYY-MM-DD_ | HR share created, GG_HR has Modify | _Result_ |
| _YYYY-MM-DD_ | IT share created, GG_IT has Modify | _Result_ |
| _YYYY-MM-DD_ | Finance share created, GG_Finance has Modify | _Result_ |
| _YYYY-MM-DD_ | Management share created, GG_Management has Modify | _Result_ |

## Security note

Lab-only test data. No real passwords, personal information, or sensitive files are stored in `C:\CompanyData`.

## References

- _Title — URL — date accessed_
