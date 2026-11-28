# Domain Password & Account Lockout Policy

Configure the Default Domain Policy's password and account lockout settings for `technova.test`, and verify them via PowerShell.

## Lab details

- **Lab:** `enterprise-infrastructure-lab`
- **Domain:** `technova.test`
- **Policy edited:** Default Domain Policy
- **Date:** 2026-11-23

> Domain-wide password/lockout policy belongs in the **Default Domain Policy** (or another domain-linked GPO) — not a department-OU-level GPO. See `gpo-fundamentals.md`.

## Part 1 — Password Policy

### Steps

1. Log into `DC01` as Domain Administrator.
2. Server Manager → **Tools** → **Group Policy Management**.
3. Expand `Forest: technova.test` → **Domains** → `technova.test`.
4. Right-click **Default Domain Policy** → **Edit**.
5. Navigate to:

```
Computer Configuration
  └── Policies
      └── Windows Settings
          └── Security Settings
              └── Account Policies
                  └── Password Policy
```

6. For each setting: double-click → **Define this policy setting** → enter the value → **Apply** → **OK**.

### Lab values used

| Setting | Lab value |
|---|---|
| Enforce password history | 5 passwords |
| Maximum password age | 90 days |
| Minimum password age | 1 day |
| Minimum password length | 12 characters |
| Password must meet complexity requirements | Enabled |
| Store passwords using reversible encryption | Disabled |

**Important:** these are practice values for learning the policy controls, not a universal modern enterprise standard. Real environments should also consider long unique passphrases, MFA, and compromised-password protection.

### Verification

```cmd
gpupdate /force
```

```powershell
Get-ADDefaultDomainPasswordPolicy
```

Check: minimum password length, password history count, complexity enabled, max/min password age.

## Part 2 — Account Lockout Policy

### Steps

Navigate to:

```
Account Policies
  └── Account Lockout Policy
```

Same procedure: double-click → define → configure → Apply → OK.

### Lab values used

| Setting | Lab value |
|---|---|
| Account lockout threshold | 5 invalid attempts |
| Account lockout duration | 15 minutes |
| Reset account lockout counter after | 15 minutes |

**Safety note:** don't deliberately enter wrong passwords repeatedly to test lockout — verify through configuration/reporting instead.

### Verification

```powershell
Get-ADDefaultDomainPasswordPolicy
```

Check: `LockoutThreshold`, `LockoutDuration`, `LockoutObservationWindow`.

## Verification log

| Date | Check | Result |
|---|---|---|
| 2026-11-23 | Password Policy configured (history 5, max age 90d, min age 1d, min length 12, complexity enabled, reversible encryption disabled) | Success |
| 2026-11-23 | `Get-ADDefaultDomainPasswordPolicy` confirms password settings | Success — `MinPasswordLength: 12`, `PasswordHistoryCount: 5`, `ComplexityEnabled: True`, `MaxPasswordAge: 90.00:00:00`, `MinPasswordAge: 1.00:00:00`, `ReversibleEncryptionEnabled: False` — all match configured values exactly |
| 2026-11-23 | Account Lockout Policy configured (threshold 5, duration 15min, reset counter 15min) | Success |
| 2026-11-23 | `Get-ADDefaultDomainPasswordPolicy` confirms `LockoutThreshold`/`LockoutDuration`/`LockoutObservationWindow` | Success — `LockoutThreshold: 5`, `LockoutDuration: 00:15:00`, `LockoutObservationWindow: 00:15:00` |

## Concepts check

- **LockoutThreshold = 5** — confirmed: the account locks after 5 consecutive failed logon attempts, tracked within the 15-minute `LockoutObservationWindow`.

## Key terms

| Term | Meaning |
|---|---|
| Password history | Number of previous passwords remembered to prevent reuse |
| Maximum/minimum password age | How long a password can/must be used before changing |
| Complexity requirements | Enforces character-variety rules on new passwords |
| Lockout threshold | Number of failed attempts before the account locks |
| Lockout duration | How long a locked account stays locked |
| Lockout observation window | Time window during which failed attempts are counted toward the threshold |

## Lessons learned

- A single Default Domain Policy applies one password/lockout standard to every account — there's no way to make exceptions at the OU level for this specific policy type. That's exactly the gap Fine-Grained Password Policies (Chapter 12) exist to fill.

## Next

Fine-Grained Password Policies (FGPP) — different password requirements for different groups of employees.

## References

- _Title — URL — date accessed_
