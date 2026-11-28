# GPO — Security Baseline (TN-HR-Security-Baseline)

> **Status: Completed and tested.** Computer account placed in `Shared-Workstations` first (general message shown), then moved to `HR-Workstations` — message changed to the HR-specific text as expected.

## Lab details

- **Lab:** `enterprise-infrastructure-lab`
- **GPO name:** `TN-HR-Security-Baseline`
- **Linked to:** HR OU
- **Date:** 2026-11-28

## Planned first exercise — interactive logon message

Navigate to:

```
Computer Configuration
└── Policies
    └── Windows Settings
        └── Security Settings
            └── Local Policies
                └── Security Options
```

Settings to configure:
- **Interactive logon: Message title for users attempting to log on**
- **Interactive logon: Message text for users attempting to log on**

Example values:

```
Title: TechNova Ltd. — Authorized Access Only
Message: This computer is intended for authorized TechNova
employees. All access is subject to company security policies.
```

## Important note (resolved)

This is a **Computer Configuration** policy. Linking it to the HR OU only takes effect if the relevant computer account actually sits inside that OU — computer accounts aren't automatically placed there based on the department of their users.

Moving `CLIENT01` between OUs confirmed that the applicable computer policy changed independently of the logged-in user.

## Verification log

| Date | Check | Result |
|---|---|---|
| 2026-11-28 | Computer account correctly placed in scope | Success |
| 2026-11-28 | Logon message appears on CLIENT01, changes correctly between Shared-Workstations (general) and HR-Workstations (HR-specific) | Success |

## References

- _Title — URL — date accessed_
