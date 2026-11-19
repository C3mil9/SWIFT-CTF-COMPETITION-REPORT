# PvP Cybersecurity Competition — Red/Blue Attack & Defense

![Status](https://img.shields.io/badge/status-completed-success)
![Placement](https://img.shields.io/badge/placement-6th%20of%2015-blue)
![Focus](https://img.shields.io/badge/focus-purple%20team%20PvP-purple)

This repository documents my experience competing in a five-hour cybersecurity PvP competition. Teams had to defend vulnerable services on their own virtual machines while using an authorized Kali Linux system to find weaknesses and capture flags on opposing systems.

The goal of this repository is to record the environment, decision-making, defensive priorities, offensive workflow, successful techniques, lessons learned, and reusable checklists.

## Results

- **Placement:** 6th out of 15 teams
- **Team:** Two competitors
- **Format:** Red/Blue attack-defense, sometimes described as Purple-vs-Purple
- **Duration:** Five hours
- **Teamwork:** Partners alternated between offense and defense
- **Availability result:** All defended servers stayed online; there were no complete outages
- **Systems:** Five vulnerable servers plus a separate Kali attack machine

## Competition Environment

| Host | Operating system | Primary service or role | Main defensive concern |
|---|---|---|---|
| `thehouse` | Windows Server 2022 | LDAP/Active Directory | Accounts, authentication, remote access, Windows hardening |
| `sunflower` | Windows Server 2019 | SMB | Shares, permissions, exposed credentials, lateral movement |
| `cherrybomb` | Ubuntu 25.04 | Pi-hole and HTTP | Web access, DNS administration, service uptime |
| `chomper` | CentOS 10 | Database | Database exposure, credentials, remote connections |
| `peashooter` | Debian 12 | SSH | Authentication, keys, root access, service configuration |
| Kali Linux | Kali Linux | Authorized attack workstation | Reconnaissance, traffic inspection, flag collection |

The environment was hosted through the competition VPN/Kamino infrastructure. Addresses, credentials, flags, and scoring-engine details are intentionally not published here.

## How We Approached the Event

### 1. Preserve uptime first

The first priority was keeping services reachable. A rushed change that broke a scoring check could cost more than leaving a low-risk issue temporarily open. We made changes incrementally and checked service health after each major action.

### 2. Inventory before changing

For each machine, we identified:

- Running services and listening ports
- Current users and privileged accounts
- Scheduled jobs and unusual processes
- Firewall state and network exposure
- Web, SSH, SMB, LDAP, and database configuration
- Suspicious files, credentials, and persistence mechanisms

### 3. Split offense and defense deliberately

One teammate monitored and hardened the defended systems while the other performed authorized reconnaissance and attacks. We alternated roles so both competitors practiced both sides of the event.

### 4. Verify every fix

After changing passwords, firewall rules, services, or permissions, we confirmed that:

- The required service still worked
- The scoring check could still reach the service
- The new credentials worked where expected
- No critical account or remote-access path was accidentally locked out

## Tools Used

- **Nmap:** Identify reachable hosts, ports, and services within the authorized competition scope
- **Wireshark:** Inspect traffic and understand authentication or service behavior
- **Kali Linux:** Authorized offensive testing and flag collection
- **SSH:** Remote administration and Linux access
- **Linux auditing commands:** Inventory processes, users, services, sockets, permissions, and scheduled tasks
- **UFW/firewalld:** Host-based firewall configuration
- **pspy:** Observe unexpected processes and commands on Linux systems
- **Bash scripts:** Repeatable inventory, hardening, and health checks

## Successful Techniques

### Defensive techniques

1. **Service-first hardening** — Protect the system without disabling the service the scoring engine depended on.
2. **Port and process inventory** — Compare listening services with the intended service list and investigate anything unexpected.
3. **Account review** — Check privileged users, weak/default credentials, remote login settings, and unauthorized accounts.
4. **Firewall review** — Restrict unnecessary inbound access while preserving required competition traffic.
5. **Process monitoring** — Use process inspection and tools such as `pspy` to identify suspicious activity or persistence.
6. **Incremental changes** — Make one change at a time, then test service health.
7. **Team communication** — Record changes and communicate before modifying a service another teammate was actively using.

### Offensive techniques

All offensive activity described here was performed only against authorized competition targets.

1. **Network reconnaissance** — Use Nmap to map available hosts, ports, and services.
2. **Traffic inspection** — Use Wireshark to identify useful protocols, authentication behavior, and plaintext data.
3. **Credential reuse testing** — When a credential was exposed in an authorized target, test whether it was reused on another permitted service.
4. **FTP-to-SSH path** — One successful path involved discovering anonymous FTP exposure, finding plaintext administrative credentials, using those credentials for SSH authentication, escalating privileges, and capturing the flag.
5. **Flag validation** — Confirm access and flag contents without changing or destroying the target service.

## Challenge Areas

### Windows systems

- Protecting LDAP/Active Directory authentication while keeping required access available
- Reviewing SMB shares and permissions
- Detecting unnecessary accounts, services, and remote-access paths
- Avoiding lockouts during password and policy changes

### Linux systems

- Hardening SSH without losing administrative access
- Reviewing firewall rules and listening sockets
- Checking cron jobs, systemd services, startup files, and unusual processes
- Protecting database and web services while maintaining uptime

### Pi-hole and HTTP

- Keeping DNS and web administration available to authorized users
- Reviewing exposed management interfaces
- Checking web-service configuration and unexpected files
- Balancing access control with the scoring engine's health checks

### Database service

- Reviewing whether the database accepted remote connections unnecessarily
- Checking accounts and authentication settings
- Separating application-required access from broad administrative access
- Testing configuration changes without interrupting dependent services

### Scoring pressure

The event rewarded uptime and successful defense checks while also rewarding flag captures. This created a constant tradeoff: harden quickly enough to block attackers, but carefully enough to avoid taking down a service or losing scoring points.

## Reusable Workflow

```text
Connect to the authorized competition network
        ↓
Inventory hosts, ports, users, services, and processes
        ↓
Protect accounts and remove obvious persistence
        ↓
Review firewall and exposed services
        ↓
Test service health and scoring checks
        ↓
Monitor for changes and suspicious activity
        ↓
Run authorized reconnaissance against opposing systems
        ↓
Document flags, fixes, and lessons learned
```

## Lessons Learned

- Uptime is a security objective in an attack-defense competition.
- Reconnaissance should happen before making broad configuration changes.
- A password change is not complete until dependent services and scoring checks are tested.
- Plaintext credentials can turn a small exposure into a full compromise when credentials are reused.
- Good role rotation matters because both offense and defense require different mindsets.
- A written change log prevents teammates from undoing or duplicating work.
- Automation is valuable, but every automated change needs a review and a rollback plan.

## Repository Contents

| Path | Purpose |
|---|---|
| [`docs/competition-overview.md`](docs/competition-overview.md) | Event format, environment, roles, and results |
| [`docs/defense-playbook.md`](docs/defense-playbook.md) | Defensive checklist for Windows and Linux systems |
| [`docs/offense-playbook.md`](docs/offense-playbook.md) | Authorized reconnaissance and attack workflow |
| [`docs/challenges-and-solutions.md`](docs/challenges-and-solutions.md) | Challenge categories and techniques that worked |
| [`docs/lessons-learned.md`](docs/lessons-learned.md) | Reflection and future improvements |
| [`scripts/README.md`](scripts/README.md) | Safe script-development notes and testing expectations |

## Responsible Use

This material is for authorized labs, competitions, and systems you own or are explicitly permitted to test. Do not scan, capture credentials from, access, or change systems outside the defined scope of an event.



