# Joining CLIENT01 to the Domain & Testing Access

Join a Windows client to `technova.test`, then verify domain login and department-share restrictions.

## Lab details

- **Lab:** `enterprise-infrastructure-lab`
- **Client:** `CLIENT01` — `192.168.50.20`
- **Domain controller / DNS:** `DC01` — `192.168.50.10`
- **Date:** 2026-11-19

## 1. VMware network

1. Shut down the client VM.
2. VM Settings → Network Adapter → **Custom** → same network as DC01 (`VMnet2`).
3. Confirm **Connected** is enabled.

## 2. Static IPv4 + DNS

| Setting | Value |
|---|---|
| IP address | `192.168.50.20` |
| Subnet mask | `255.255.255.0` |
| Default gateway | Leave blank |
| Preferred DNS | `192.168.50.10` |

**Important:** the client must use DC01 as its DNS server, not a public DNS — domain clients rely on the DC's DNS to locate AD services.

## 3. Rename the client

1. System Properties → **Change settings** → **Change**.
2. Computer name: `CLIENT01`.
3. Stay in a workgroup for now → restart.

## 4. Pre-join connectivity checks

```cmd
ping 192.168.50.10
nslookup technova.test
nslookup DC01.technova.test
```

DNS queries should resolve to the domain controller. **Stop and troubleshoot DNS before joining if any of these fail.**

## 5. Join the domain

1. System Properties → **Change settings** → **Change**.
2. **Member of** → **Domain** → enter `technova.test` → **OK**.
3. When prompted, authenticate with `TECHNOVA\Administrator` — **used only to authorize the domain join itself, not as a day-to-day or test login.**
4. Expect a welcome message to the domain.
5. Restart `CLIENT01`.

**Note:** Domain joining connects a computer to Active Directory so it can use centrally managed identities and policies.

## 6. Test A — employee login

1. On `CLIENT01`, sign out → **Other user**.
2. Sign in as a test employee account (e.g. `TECHNOVA\Sarah`) — **never the Administrator account** for this or any later login test/screenshot.
3. Successful login confirms domain authentication is working.

## 7. Test B — department share access

`Win + R` →

```
\\DC01\HR
\\DC01\Finance
\\DC01\IT
\\DC01\Management
```

Try each with the matching department's test employee account.

## 8. Test C — cross-department restriction

Example expectations:

| Account | Should access | Should NOT access |
|---|---|---|
| Sarah (HR) | HR | Finance |
| Ali (IT) | IT | HR |

If a user *can* reach another department's folder, investigate (in order): group membership → NTFS permissions → share permissions. Remember share and NTFS permissions combine rather than one simply overriding the other — check both layers.

## Verification log

| Date | Check | Result |
|---|---|---|
| 2026-11-19 | CLIENT01 joined `technova.test` (via `TECHNOVA\Administrator`, join only) | _Result_ |
| 2026-11-19 | Login as `TECHNOVA\Sarah` | _Result_ |
| 2026-11-19 | HR account → `\\DC01\HR` | _Result_ |
| 2026-11-19 | HR account → `\\DC01\Finance` (expect deny) | _Result_ |
| 2026-11-19 | IT account → `\\DC01\IT` | _Result_ |
| 2026-11-19 | IT account → `\\DC01\HR` (expect deny) | _Result_ |

## Lessons learned

- _What did you learn about domain-join DNS dependency, or access restriction debugging?_

## References

- _Title — URL — date accessed_
