# Active Directory Domain Services — Role Installation

Install the AD DS role on `DC01` (`192.168.50.10`) in preparation for promoting it to a domain controller. Role installation and domain controller promotion are separate steps; this page covers role installation only.

## Lab details

- **Lab:** `enterprise-infrastructure-lab`
- **Server:** `DC01` — `192.168.50.10`
- **Planned domain:** `technova.test`
- **Date completed:** 2026-11-17

> **Prerequisite:** Complete [server preparation](../Windows-Server/Server-Preparation.md). A pre-install snapshot is useful for rollback, but optional.

## Install the role

1. Sign in to `DC01` and open **Server Manager**.
2. Select **Manage → Add Roles and Features**.
3. Choose **Role-based or feature-based installation**.
4. Select **Select a server from the server pool**, choose `DC01`, and continue.
5. Check **Active Directory Domain Services** and select **Add Features** when prompted.
6. Leave default features selected unless you have a reason to change them. Continue through the wizard pages.
7. Review the selected role and features, then select **Install**.

**Expected result:** The AD DS role installs successfully.

> **Important:** Installing the role does not make the server a domain controller. Promotion is documented in [Domain Controller Setup](Domain-Controller-Setup.md).

## Installation log

| Date | Action | Result |
|---|---|---|
| 2026-11-17 | Installed the AD DS role on `DC01` before promotion | Installation completed; `DC01` was subsequently promoted to a domain controller. |

## Key terms

| Term | Meaning |
|---|---|
| Role | A major server function, such as AD DS or DNS |
| Feature | Supporting Windows functionality |
| AD DS | Directory service for managing identities, computers, groups, and authentication |
| Domain controller | A server that stores and provides Active Directory data |

## References

- _Title — URL — date accessed_
