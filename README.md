# Enterprise Infrastructure Lab

A practical, research-driven notebook for learning enterprise infrastructure in an isolated, authorized lab. Use it to plan experiments, capture results, and keep useful references in one place.

## At a glance

| Area | Notes |
|---|---|
| Purpose | _What do you want to learn or build?_ |
| Lab environment | _Hypervisor/cloud, systems, and versions; omit sensitive details_ |
| Started | _YYYY-MM-DD_ |
| Current focus | _Topic or experiment in progress_ |

## Learning path

- [ ] [Networking](01-networking/networking-notes.md)
- [ ] [Windows Server preparation](02-windows-server/server-preparation.md)
- [ ] [AD DS installation](03-active-directory/ad-ds-installation.md)
- [ ] [Domain controller setup](03-active-directory/domain-controller-setup.md)
- [ ] [Users and groups](03-active-directory/users-and-groups.md)
- [ ] [Group Policy](03-active-directory/group-policy.md)
- [ ] [Department file shares](04-clients-and-shares/file-shares.md)
- [ ] [Client domain join and access testing](04-clients-and-shares/client-join-and-testing.md)

Mark a topic complete when you have documented what you learned, how you verified it, and any useful follow-up work.

## Repository map

```text
enterprise-infrastructure-lab/
├── README.md
├── .gitignore
├── 01-networking/
│   └── networking-notes.md
├── 02-windows-server/
│   └── server-preparation.md
├── 03-active-directory/
│   ├── ad-ds-installation.md
│   ├── domain-controller-setup.md
│   ├── users-and-groups.md
│   └── group-policy.md
├── 04-clients-and-shares/
│   ├── file-shares.md
│   └── client-join-and-testing.md
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
