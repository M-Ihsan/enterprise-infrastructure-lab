# GPO — Security Baseline (TN-HR-Security-Baseline)

> **Status: introduced, not yet completed.** This file documents the planned exercise; fill in results once it's actually built and tested.

## Lab details

- **Lab:** `enterprise-infrastructure-lab`
- **GPO name:** `TN-HR-Security-Baseline`
- **Linked to:** HR OU (planned)
- **Date:** 2026-11-19

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

## Important caveat (not yet resolved)

This is a **Computer Configuration** policy. Linking it to the HR OU only takes effect if the relevant computer account actually sits inside that OU — computer accounts aren't automatically placed there just because a GPO is linked. Computer placement and policy scope need to be handled properly as a separate step before this policy can be meaningfully tested. **Do not move computer accounts around just to force this policy to apply** — get the OU design right first.

## Planned verification

| Date | Check | Result |
|---|---|---|
| 2026-11-19 | Computer account correctly placed in scope | Verified and Tested |
| 2026-11-19 | Logon message appears on CLIENT01 | Verified and Tested |

## References

- _Title — URL — date accessed_
