
# Fine-Grained Password Policies (FGPP)

Apply stricter password/lockout requirements to a specific group (IT administrators) without creating a separate domain, using a Password Settings Object (PSO).

## Lab details

- **Lab:** `enterprise-infrastructure-lab`
- **Domain:** `technova.test`
- **Group:** `GG_IT_Admins`
- **PSO:** `PSO_IT_Admins`
- **Date:** 2026-11-23

## Concept

The domain's Default Domain Policy sets one password policy for everyone. FGPP lets different password/lockout settings apply to specific **users or global security groups** within the same domain — implemented via **Password Settings Objects (PSOs)**.

**Important:** FGPP applies directly to users and global security groups, **not OUs**.

```
technova.test
   │
   ├── GG_HR
   ├── GG_IT
   ├── GG_Finance
   ├── GG_Management
   │
   └── GG_IT_Admins
           │
           └── PSO_IT_Admins
```

The PSO lives in AD's **Password Settings Container** and is assigned directly to the group — it is not linked to the group's OU the way a GPO would be.

This is a lab demonstration, not a universal enterprise password standard.

## Part 1 — Create the IT administrator group

1. Server Manager → **Tools** → **Active Directory Users and Computers**.
2. Expand `technova.test` → `Security Groups` OU → right-click → **New** → **Group**.

| Setting | Value |
|---|---|
| Group name | `GG_IT_Admins` |
| Group scope | Global |
| Group type | Security |

3. Add an existing IT test account (not the Domain Administrator account) as a member:
   - Double-click `GG_IT_Admins` → **Members** tab → **Add** → enter username → **Check Names** → **OK** → **Apply**.

## Part 2 — Create the Password Settings Object

1. Server Manager → **Tools** → **Active Directory Administrative Center**.
2. Expand `technova.test` → **System** → **Password Settings Container**.
3. Tasks pane → **New** → **Password Settings**.

### PSO configuration

| Setting | Value |
|---|---|
| Name | `PSO_IT_Admins` |
| Precedence | `10` |
| Minimum password length | `14` |
| Enforce password history | `10` |
| Password complexity | Enabled |
| Reversible encryption | Disabled |
| Minimum password age | 1 day |
| Maximum password age | 90 days |
| Lockout threshold | 5 |
| Lockout duration | 15 minutes |
| Reset lockout counter | 15 minutes |

**Precedence:** decides which PSO wins when multiple applicable PSOs conflict for the same user/group. **Lower number = higher priority** (precedence 10 beats precedence 20).

## Part 3 — Assign the policy to the group

In the same Password Settings editor:

1. **Directly Applies To** → **Add**.
2. Enter `GG_IT_Admins` → **Check Names** → confirm.
3. **OK** to create the policy.

## Part 4 — Verify

```powershell
Get-ADFineGrainedPasswordPolicy -Filter *
Get-ADFineGrainedPasswordPolicySubject -Identity PSO_IT_Admins
Get-ADUserResultantPasswordPolicy -Identity <IT test username>
```

Expect `PSO_IT_Admins` to show as the resultant policy for the IT test account. If blank or unexpected, don't change anything yet — troubleshoot the group assignment and precedence first.

## Verification log

| Date | Check | Result |
|---|---|---|
| 2026-11-23 | `GG_IT_Admins` group created, IT test account (`hencejohn`) added | Success |
| 2026-11-23 | `PSO_IT_Admins` created with lab values above | Success |
| 2026-11-23 | PSO assigned to `GG_IT_Admins` via Directly Applies To | Success |
| 2026-11-23 | `Get-ADFineGrainedPasswordPolicy -Filter *` shows `PSO_IT_Admins` | Success — Precedence 10, MinPasswordLength 14, PasswordHistoryCount 10, ComplexityEnabled True, ReversibleEncryptionEnabled False, LockoutThreshold 5, LockoutDuration/ObservationWindow 00:15:00, MaxPasswordAge 90d, MinPasswordAge 1d |
| 2026-11-23 | `Get-ADFineGrainedPasswordPolicySubject -Identity PSO_IT_Admins` shows `GG_IT_Admins` | Success — AppliesTo `GG_IT_Admins`, correct SID/DN |
| 2026-11-23 | `Get-ADUserResultantPasswordPolicy -Identity hencejohn` resolves to `PSO_IT_Admins` | Success — AppliesTo `{GG_IT_Admins, OU=Security Groups, OU=TechNova, DC=technova, DC=test}`, all values match the PSO |

## Concepts check

1. Does FGPP apply directly to OUs? — _No — directly to users or global security groups._
2. What does PSO stand for? — _Password Settings Object._
3. Which policy wins: precedence 10 or precedence 20? — _Precedence 10 (lower number = higher priority)._
4. If an IT administrator is a member of `GG_IT_Admins`, which password policy should apply? — _`PSO_IT_Admins`, overriding the Default Domain Policy for that user._

## Key terms

| Term | Meaning |
|---|---|
| FGPP | Fine-Grained Password Policy — lets multiple password policies coexist in one domain |
| PSO | Password Settings Object — the object implementing an FGPP |
| Precedence | Tie-breaker when multiple PSOs apply; lower number wins |
| Password Settings Container | Where PSOs are stored in AD (not an OU) |

## Lessons learned

- _What did you learn about how FGPP differs from GPO-based password policy (Chapter 11)?_

## Next

Windows security hardening — Windows Defender, Firewall, security baselines, Windows LAPS.

## References

- [Configure Fine-Grained Password Policies](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/get-started/adac/fine-grained-password-policies) — Microsoft Learn — accessed 2026-11-23
