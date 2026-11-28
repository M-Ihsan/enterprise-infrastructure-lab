# Computer OUs & GPO Scope — User vs Computer Policy

Understand why Computer Configuration policies follow the computer, not the logged-in user, then build a computer OU structure and test GPO scope/precedence with it.

## Lab details

- **Lab:** `enterprise-infrastructure-lab`
- **Date:** 2026-11-20

## Core concept

**User-based policies follow the user. Computer-based policies follow the computer.**

Example: the earlier interactive-logon message GPO lives under **Computer Configuration**, so it follows the computer's OU — not the signed-in employee's department. A user-based policy (e.g. Sarah's department) follows the user account instead.

### Expected scenario

| Computer | Logged-in user | Expected message |
|---|---|---|
| HR-PC01 | Sarah (HR) | HR message |
| HR-PC01 | Ali (IT) | HR message |
| LOBBY-PC01 | Sarah (HR) | General message |
| LOBBY-PC01 | Ali (IT) | General message |

The message follows the **computer**, even when a different department's employee signs in.

## Key terms

| Concept | Meaning |
|---|---|
| User Configuration | Settings associated with the user |
| Computer Configuration | Settings associated with the computer |
| OU placement | Determines which OU-linked policies apply |
| Loopback Processing | Advanced feature — lets the computer's location override/merge user policy (not yet covered in depth) |

## 1. Build the computer OU structure

Under the existing `TechNova` OU, inside `Computers`:

```
TechNova
│
├── Employees
│
├── Departments
│   ├── HR
│   ├── IT
│   ├── Finance
│   └── Management
├── Computers
│   ├── Workstations
│   │   ├── HR-Workstations
│   │   ├── IT-Workstations
│   │   ├── Finance-Workstations
│   │   ├── Management-Workstations
│   │   └── Shared-Workstations
│   │
│   └── Servers
│
└── Security Groups
```

**Note:** Computer objects represent domain-joined devices in AD. Organizing them into OUs makes computer-specific administration (and GPO targeting) possible.

## 2. Move CLIENT01 into its OU

1. Active Directory Users and Computers → default `Computers` container.
2. Locate `CLIENT01` → right-click → **Move**.
3. Select `TechNova → Computers → Workstations → Shared-Workstations`.
4. Confirm.

CLIENT01 acts as the shared workstation for this exercise.

## 3. General employee notice GPO

Linked at `TechNova → Computers → Workstations` (applies to all workstation child OUs):

- **Name:** `TN-GPO-General-Employee-Notice`
- **Path:** `Computer Configuration → Policies → Windows Settings → Security Settings → Local Policies → Security Options`
- **Setting:** Interactive logon: Message title / Message text

```
Title: TechNova Ltd. — Employee Notice
Message: This workstation is provided for authorized
TechNova employees. Use of this system is
subject to company security policies.
```

## 4. HR-specific computer notice GPO

Linked at `TechNova → Computers → Workstations → HR-Workstations` (lower in the hierarchy):

- **Name:** `TN-GPO-HR-Workstation-Notice`
- **Same path/setting as above**

```
Title: TechNova Ltd. — HR Department
Message: This workstation is assigned to the
Human Resources department.
Authorized use only.
```

**Precedence note:** the HR-specific GPO is linked lower in the OU hierarchy, so its conflicting settings take precedence over the inherited general message — provided both GPOs apply normally and no higher-precedence modifier changes that result.

## 5. Testing

Only one Windows client exists for now, so test by moving CLIENT01 between the two computer OUs:

1. Move `CLIENT01` into the OU being tested (`Shared-Workstations` or `HR-Workstations`).
2. `gpupdate /force`.
3. Restart the client.
4. Check the logon message shown.

## Verification log

| Date | Check | Result |
|---|---|---|
| 2026-11-20 | Computer OU structure created under `Computers` | Success |
| 2026-11-20 | `CLIENT01` moved to `Shared-Workstations` | Success |
| 2026-11-20 | `TN-GPO-General-Employee-Notice` linked to `Workstations`, message shows on CLIENT01 | Success |
| 2026-11-20 | `CLIENT01` moved to `HR-Workstations` | Success |
| 2026-11-20 | `TN-GPO-HR-Workstation-Notice` message overrides general message | Success |

## Not yet covered (upcoming modules)

- GPO inheritance and precedence in full (LSDOU, enforcement, filtering)
- Group Policy Loopback Processing
- Domain password/account lockout policy, fine-grained password policies
- Windows security hardening (Defender, Firewall, auditing, Windows LAPS)
- PowerShell-based AD administration
- AD security assessment (delegation, privileged accounts, misconfigurations)

## Lessons learned

- Computer Configuration policies follow the computer account's OU, so moving the computer changes which policy scope applies even if the logged-in user remains unchanged.

## References

- _Title — URL — date accessed_
