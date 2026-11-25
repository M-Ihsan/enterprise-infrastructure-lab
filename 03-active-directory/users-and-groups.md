# Users and Groups — TechNova Ltd.

Build a fictional small-enterprise AD structure: OUs, security groups, and test employee accounts.

## Lab details

- **Lab:** `enterprise-infrastructure-lab`
- **Domain:** `technova.test`
- **Fictional company:** TechNova Ltd.
- **Date:** 2026-11-19

## Goal and scope

Practice organizing AD objects the way a real small enterprise would: an OU hierarchy for departments, global security groups mapped to those departments, and test user accounts assigned to the correct group.

## 1. Organizational Units (OUs)

**OU** = a container in Active Directory used to organize users, computers and groups. Group Policy can be applied at the OU level.

### Structure created

```
technova.test
│
└── TechNova
    ├── Employees
    ├── Departments
    │   ├── HR
    │   ├── IT
    │   ├── Finance
    │   └── Management
    ├── Computers
    └── Security Groups
```

### Steps

1. Server Manager → **Tools** → **Active Directory Users and Computers**.
2. Expand `technova.test`.
3. Right-click the domain → **New** → **Organizational Unit**.
4. Repeat to build the hierarchy above, with `TechNova` as the parent OU.

## 2. Security groups

**Security group** — lets admins assign permissions to a collection of users instead of configuring each user individually.
**Global group** — commonly used to collect users from the same domain.

Created inside the `Security Groups` OU:

| Group name | Purpose | Scope | Type |
|---|---|---|---|
| GG_HR | HR department access | Global | Security |
| GG_IT | IT department access | Global | Security |
| GG_Finance | Finance department access | Global | Security |
| GG_Management | Management access | Global | Security |

## 3. Employee accounts

Fictional test accounts only — no real personal data.

| Department | Test accounts |
|---|---|
| HR | Sarah, Ahmed |
| IT | Ali, John |
| Finance | Emma, David |
| Management | Michael, Sophia |

### Steps

1. Active Directory Users and Computers → expand `TechNova → Departments`.
2. Right-click the department OU → **New** → **User**.
3. Enter first name, last name, username.
4. Set a lab-only password (never reused from a real account).
5. Repeat for each department.

### Group assignment

1. Open the employee's **Properties**.
2. **Member Of** tab → **Add**.
3. Enter the matching group name (e.g. `GG_HR` for Sarah) → **Check Names** → **Apply**.

## Procedure and results

| Date | Action | Result |
|---|---|---|
| 2026-11-19 | Created `TechNova` OU hierarchy | _Success / issues_ |
| 2026-11-19 | Created GG_HR, GG_IT, GG_Finance, GG_Management | _Success / issues_ |
| 2026-11-19 | Created 8 test employee accounts, assigned to groups | _Success / issues_ |

## Security note

Passwords for these test accounts are **not** recorded in this repo or in any screenshot. Stored separately, lab-only, never reused elsewhere.

## Lessons learned

- _What did you learn about OU design, group scope, or account creation?_

## References

- _Title — URL — date accessed_
