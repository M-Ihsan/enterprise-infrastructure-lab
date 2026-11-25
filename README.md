# Enterprise Infrastructure Lab

A practical, research-driven notebook for learning enterprise infrastructure in an isolated, authorized lab. Use it to plan experiments, capture results, and keep useful references in one place.

## At a glance

| Area | Notes |
|---|---|
| Purpose | Practical Active Directory / enterprise infrastructure lab — networking, AD DS, Group Policy, file shares, and Windows security hardening on an isolated VMware lab |
| Lab environment | VMware Workstation, Windows Server 2012 R2 (DC01), Windows 10 (CLIENT01), host-only VMnet2, domain `technova.test` |
| Started | 2026-11-16 |
| Current focus | Windows security hardening (Chapter 13) |

## Learning path

- [ ] [Active Directory notebook (consolidated reference)](active-directory-notebook.md)
- [ ] [Networking](01-networking/networking-notes.md)
- [ ] [IPv4 vs IPv6](01-networking/ipv4-vs-ipv6.md)
- [ ] [Windows Server preparation](02-windows-server/server-preparation.md)
- [ ] [AD DS installation](03-active-directory/ad-ds-installation.md)
- [ ] [Domain controller setup](03-active-directory/domain-controller-setup.md)
- [ ] [Users and groups](03-active-directory/users-and-groups.md)
- [ ] [Department file shares](04-clients-and-shares/file-shares.md)
- [ ] [Client domain join and access testing](04-clients-and-shares/client-join-and-testing.md)
- [ ] [GPO fundamentals](05-group-policy/gpo-fundamentals.md)
- [ ] [HR drive mapping](05-group-policy/hr-drive-mapping.md)
- [ ] [Security baseline](05-group-policy/security-baseline.md)
- [ ] [Computer OUs and GPO scope](05-group-policy/computer-ou-and-gpo-scope.md)
- [ ] [GPO inheritance and precedence](05-group-policy/gpo-inheritance-and-precedence.md)
- [ ] [Password policy](05-group-policy/password-policy.md)
- [ ] [Fine-Grained Password Policies](05-group-policy/fine-grained-password-policies.md)
- [ ] [GPO troubleshooting](05-group-policy/troubleshooting-gpo.md)
- [ ] [Windows Firewall hardening](06-security-hardening/windows-firewall.md)
- [ ] [DNS and network profile troubleshooting](06-security-hardening/dns-and-network-profile-troubleshooting.md)

Mark a topic complete when you have documented what you learned, how you verified it, and any useful follow-up work.

## Repository map

```text
enterprise-infrastructure-lab/
├── README.md
├── active-directory-notebook.md
├── .gitignore
├── 01-networking/
│   ├── networking-notes.md
│   └── ipv4-vs-ipv6.md
├── 02-windows-server/
│   └── server-preparation.md
├── 03-active-directory/
│   ├── ad-ds-installation.md
│   ├── domain-controller-setup.md
│   └── users-and-groups.md
├── 04-clients-and-shares/
│   ├── file-shares.md
│   └── client-join-and-testing.md
├── 05-group-policy/
│   ├── gpo-fundamentals.md
│   ├── hr-drive-mapping.md
│   ├── security-baseline.md
│   ├── computer-ou-and-gpo-scope.md
│   ├── gpo-inheritance-and-precedence.md
│   ├── password-policy.md
│   ├── fine-grained-password-policies.md
│   └── troubleshooting-gpo.md
├── 06-security-hardening/
│   ├── windows-firewall.md
│   └── dns-and-network-profile-troubleshooting.md
└── screenshots/
    └── README.md
```

## How to use this repository

1. Start with the topic template and write down the question or goal.
2. Note the lab setup and relevant versions so you can reproduce the experiment.
3. Record what you changed, what you expected, and what actually happened.
4. Add references and link screenshots or diagrams where they help explain the result.
5. Update the learning path and capture follow-up questions.

## Lab safety and privacy

- Work only in systems and environments you own or are explicitly authorized to use.
- Keep experiments isolated from production networks unless you have approval and a safe plan.
- Never commit passwords, tokens, private keys, recovery codes, product keys, or sensitive organizational information.
- Review screenshots and diagrams for credentials, personal information, and identifying network details before committing.
- Prefer official vendor documentation and record the date you accessed each source.

## Notes

The pages in this repository are starter templates. Replace the prompts with your own research, decisions, procedures, results, and lessons learned.
