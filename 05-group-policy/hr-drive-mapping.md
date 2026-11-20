# GPO — HR Drive Mapping (TN-HR-Drive-Mapping)

First practical GPO: automatically map the HR department's share as drive `H:` for HR employees.

## Lab details

- **Lab:** `enterprise-infrastructure-lab`
- **GPO name:** `TN-HR-Drive-Mapping`
- **Linked to:** HR OU
- **Date:** _YYYY-MM-DD_

## 1. Create and link the GPO

1. In Group Policy Management, right-click the **HR** OU.
2. **Create a GPO in this domain, and Link it here**.
3. Name it `TN-HR-Drive-Mapping` → OK.
4. Right-click the new GPO → **Edit**.

**Note:** Linking a GPO to an OU makes its settings available to objects within that OU.

## 2. Configure the mapped drive

Navigate to:

```
User Configuration
└── Preferences
    └── Windows Settings
        └── Drive Maps
```

Right-click **Drive Maps** → **New** → **Mapped Drive**, then set:

| Setting | Value |
|---|---|
| Action | Update |
| Location | `\\DC01\HR` |
| Label as | HR Shared |
| Drive letter | H: |
| Reconnect | Checked |

Apply → OK → close the editor.

**Notes:**
- **Drive mapping** = connecting a network folder to a drive letter.
- **Update** = creates the mapping if missing, updates it if it already exists.
- **UNC path** = network resource address, e.g. `\\DC01\HR`.

## 3. Test on CLIENT01

1. Sign in as an HR test employee (e.g. Sarah).
2. `gpupdate /force` in Command Prompt.
3. Sign out and back in.
4. File Explorer → **This PC** → look for `HR Shared (H:)`.

### Expected behavior

- HR employee (Sarah) sees `H:` and can access the HR share.
- Sarah should **not** be able to access Finance.
- An IT employee should **not** automatically receive the HR drive mapping.

### If the drive doesn't appear

```cmd
gpresult /r
```

For a detailed report:

```cmd
gpresult /h C:\gp-report.html
```

Open the HTML file in a browser to see exactly which policies are applying (and which aren't).

## Verification log

| Date | Check | Result |
|---|---|---|
| _YYYY-MM-DD_ | `TN-HR-Drive-Mapping` created and linked to HR OU | _Result_ |
| _YYYY-MM-DD_ | `gpupdate /force` on CLIENT01 | _Result_ |
| _YYYY-MM-DD_ | HR employee sees `H:` drive | _Result_ |
| _YYYY-MM-DD_ | HR employee can access `\\DC01\HR` via H: | _Result_ |
| _YYYY-MM-DD_ | HR employee denied Finance access | _Result_ |
| _YYYY-MM-DD_ | IT employee does NOT receive H: mapping | _Result_ |
| _YYYY-MM-DD_ | `gpresult /h` report generated and reviewed | _Result_ |

## Lessons learned

- _What did you learn about GPO scope, OU targeting, or Preferences vs Policies settings?_

## References

- _Title — URL — date accessed_
