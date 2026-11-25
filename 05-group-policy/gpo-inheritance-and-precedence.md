# GPO Inheritance & Precedence (LSDOU)

How Group Policy decides which setting wins when multiple GPOs apply to the same computer or user.

## Lab details

- **Lab:** `enterprise-infrastructure-lab`
- **Date:** 2026-11-21

## Processing order — LSDOU

1. **Local** — policy configured on the individual computer
2. **Site** — policies linked to an AD site
3. **Domain** — policies linked to the domain
4. **OU** — policies linked to an Organizational Unit
5. **Child OU** — policies linked to nested OUs, processed later

Later-processed, applicable settings take precedence when they conflict with earlier ones.

## Two separate precedence mechanisms — don't conflate them

### A. Multiple GPOs linked to the SAME OU → link order decides

Example, both linked to `HR-Workstations`:

| GPO | Link order |
|---|---|
| TN-GPO-HR-Test | 1 |
| TN-GPO-HR-Workstation-Notice | 2 |

**Link order 1 has the highest precedence** among GPOs linked at the same container, for settings they both configure.

### B. GPOs linked at DIFFERENT OU levels → child OU normally wins

```
Workstations
    │
    ├── TN-General-Notice
    │
    └── HR-Workstations
             │
             └── TN-HR-Notice
```

The child OU's GPO is processed later in LSDOU order, so its conflicting setting normally wins over the parent's — this is **inheritance + processing order**, a different mechanism from link-order-within-one-OU above.

**Important:** different GPOs configuring *different* settings never conflict at all — a conflict only exists when multiple applicable GPOs configure the *same* setting differently.

## Modifiers to these rules

| Feature | What it does |
|---|---|
| Link order 1 | Highest precedence among GPO links at the same container |
| Inheritance | Lets applicable parent policies flow down to child OUs |
| Block Inheritance | Stops normal inherited GPOs from applying to an OU |
| Enforced | Protects a GPO link from Block Inheritance and forces precedence over conflicting lower-level settings |
| Security Filtering | Restricts which users/computers a GPO actually applies to |

Don't enable Block Inheritance, Enforced, or Security Filtering on existing policies just to experiment — practice these individually, deliberately.

## Practical exercise — testing precedence

1. In `Workstations` OU: create `TN-GPO-General-Test`, set interactive logon title to `GENERAL POLICY`, link it.
2. In `HR-Workstations` OU: create `TN-GPO-HR-Test`, set interactive logon title to `HR POLICY`, link it.
3. Move `CLIENT01` into `HR-Workstations` → `gpupdate /force` → restart → expect **HR POLICY**.
4. Move `CLIENT01` into `Shared-Workstations` → refresh → restart → expect **GENERAL POLICY**.

This demonstrates child-OU inheritance precedence (mechanism B above), not link-order precedence (mechanism A) — those are tested differently (multiple GPOs on the *same* OU, reordered).

## Lab GPO inventory — don't delete, just manage

Keep all of these; disable/unlink finished test GPOs rather than deleting, so the environment stays understandable:

- `TN-GPO-General-Employee-Notice` — general message, linked to `Workstations`
- `TN-GPO-HR-Workstation-Notice` — HR-specific message, linked to `HR-Workstations`
- `TN-HR-Drive-Mapping` — HR drive mapping
- `TN-GPO-General-Test`, `TN-GPO-HR-Test` — precedence-testing GPOs

## Troubleshooting commands

```cmd
gpupdate /force
gpresult /r
gpresult /h C:\gpo-report.html
```

```powershell
Get-GPO -All
```

**Note:** GPO troubleshooting means identifying *why* a policy did or didn't apply to a specific user/computer — `gpresult /h` is the most reliable way to see the actual effective result, not just what's linked.

## Verification log

| Date | Check | Result |
|---|---|---|
| 2026-11-21 | `TN-GPO-General-Test` created, linked to `Workstations` | _Result_ |
| 2026-11-21 | `TN-GPO-HR-Test` created, linked to `HR-Workstations` | _Result_ |
| 2026-11-21 | CLIENT01 in `HR-Workstations` → logon title shows `HR POLICY` | _Result_ |
| 2026-11-21 | CLIENT01 moved to `Shared-Workstations` → logon title shows `GENERAL POLICY` | _Result_ |
| 2026-11-21 | `gpresult /h` report reviewed for effective settings | _Result_ |

## Lessons learned

- _What's the actual difference you observed between link-order precedence and OU-inheritance precedence?_

## References

- _Title — URL — date accessed_
