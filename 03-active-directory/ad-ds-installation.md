# Active Directory Domain Services — Role Installation

Install the AD DS role on `DC01` (`192.168.50.10`) in preparation for promoting it to a domain controller. Role installation and domain controller promotion are separate steps; this page covers role installation only.

## Lab details

- **Lab:** `enterprise-infrastructure-lab`
- **Server:** `DC01` — `192.168.50.10`
- **Planned domain:** `technova.test`
- **Date completed:** 2026-11-17

> **Prerequisite:** Complete the server preparation documented in [`server-preparation.md`](../02-windows-server/server-preparation.md). A pre-install snapshot is useful for rollback, but optional. Without one, you can still troubleshoot or undo changes; you simply will not have that snapshot as a rollback point.

## 1. Open Server Manager

1. Sign in to `DC01`.
2. Open **Server Manager**.
3. Select **Manage → Add Roles and Features**.

## 2. Choose the installation type

1. In the wizard, select **Next**.
2. Choose **Role-based or feature-based installation**.
3. Select **Next**.

**Key terms:**

- **Role:** a major server function, such as AD DS or DNS.
- **Feature:** an additional Windows capability that supports server functionality.

## 3. Select the destination server

1. Choose **Select a server from the server pool**.
2. Select `DC01` and confirm the server name.
3. Select **Next**.

## 4. Select Active Directory Domain Services

1. Check **Active Directory Domain Services**.
2. Select **Add Features** when prompted.
3. Select **Next**.

**Note:** AD DS is the Windows Server role that provides directory services for identities, computers, groups, and authentication.

## 5. Review features

1. Leave the default features selected unless you have a reason to change them.
2. Select **Next** through the features and AD DS information pages.

## 6. Install the role

1. Review the selected role and features.
2. Leave automatic restart disabled unless you specifically want to allow an unattended restart.
3. Select **Install** and wait for completion.

**Expected result:** The AD DS role installs successfully.

> **Important:** Installing the role does not make the server a domain controller. Promotion is documented in [`domain-controller-setup.md`](domain-controller-setup.md).

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
