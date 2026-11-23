# Domain Password & Account Lockout Policy

Configure the Default Domain Policy's password and account lockout settings for `technova.test`, and verify them via PowerShell.

## Lab details

- **Lab:** `enterprise-infrastructure-lab`
- **Domain:** `technova.test`
- **Policy edited:** Default Domain Policy
- **Date:** _YYYY-MM-DD_

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

**Important:** these are practice values for learning the policy controls, not a universal modern enterprise standard. Real environments should also consider long unique passphrases, MFA, and compromised-password protection (e.g. Azure AD Password Protection).

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
| _YYYY-MM-DD_ | Password Policy configured (history 5, max age 90d, min age 1d, min length 12, complexity enabled, reversible encryption disabled) | _Result_ |
| _YYYY-MM-DD_ | `Get-ADDefaultDomainPasswordPolicy` confirms password settings | _Result_ |
| _YYYY-MM-DD_ | Account Lockout Policy configured (threshold 5, duration 15min, reset counter 15min) | _Result_ |
| _YYYY-MM-DD_ | `Get-ADDefaultDomainPasswordPolicy` confirms `LockoutThreshold`/`LockoutDuration`/`LockoutObservationWindow` | _Result_ |

## Concepts check

- **LockoutThreshold = 5** — _write your own answer here once you've confirmed it, e.g.: the account locks after 5 consecutive failed logon attempts within the observation window._

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

- _What did you learn about domain password/lockout policy scope and verification?_

## Next

Fine-Grained Password Policies (FGPP) — different password requirements for different groups of employees.

## References

- _Title — URL — date accessed_
