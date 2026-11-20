# Group Policy Fundamentals

Core concepts behind Group Policy before building TechNova's actual policies.

## Lab details

- **Lab:** `enterprise-infrastructure-lab`
- **Domain:** `technova.test`
- **Date:** _YYYY-MM-DD_

## What Group Policy is for

Centrally configures Windows users and computers from the domain controller. For TechNova, used to:

1. Map department network drives automatically.
2. Configure password and account policies.
3. Apply Windows security settings.
4. Configure auditing.
5. Manage desktop and user restrictions.
6. Apply different policies to different departments.

## Key terms

| Term | Meaning |
|---|---|
| GPO | Group Policy Object — a collection of configuration settings |
| GPMC | Group Policy Management Console |
| GPO Link | Connects a GPO to a Site, Domain, or OU |
| User Configuration | Settings applied to users |
| Computer Configuration | Settings applied to computers |
| gpupdate | Refreshes Group Policy settings on a client |
| gpresult | Shows which policies apply to a given user/computer |

## Opening Group Policy Management

On `DC01`:

1. Server Manager → **Tools** → **Group Policy Management**.
2. Expand:

```
Forest: technova.test
└── Domains
    └── technova.test
        └── TechNova
            └── Departments
                ├── HR
                ├── IT
                ├── Finance
                └── Management
```

Confirm the department OUs are visible before linking any GPOs.

## Important distinction

Domain-wide password and account-lockout policies are normally configured in the **Default Domain Policy** (or another domain-linked password policy) — not applied at department-OU level and expected to override domain settings. Fine-grained password policies are a separate, more advanced option.

## References

- _Title — URL — date accessed_
