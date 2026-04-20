# 🎫 ITSM Helpdesk Lab — osTicket with Active Directory Integration

> A fully functional IT Service Management system built on **osTicket** — deployed on Ubuntu 25,
> integrated with the `InfoTech.com` Active Directory domain, and populated with real tickets
> based on incidents from the [AD & Windows Server Labs](https://github.com/your-username/ad-windows-server-labs)
> and [Wazuh SIEM Lab](https://github.com/your-username/wazuh-siem-lab) projects.

<div align="center">

![osTicket](https://img.shields.io/badge/osTicket-1.18.x-blue?style=flat-square)
![Ubuntu](https://img.shields.io/badge/Ubuntu-25-E95420?style=flat-square&logo=ubuntu)
![PHP](https://img.shields.io/badge/PHP-8.x-777BB4?style=flat-square&logo=php)
![MariaDB](https://img.shields.io/badge/MariaDB-10.x-003545?style=flat-square&logo=mariadb)
![Active Directory](https://img.shields.io/badge/Domain-InfoTech.com-darkblue?style=flat-square)
![Status](https://img.shields.io/badge/Status-In%20Progress-yellow?style=flat-square)

</div>

---

## 📌 Overview

Every IT support role runs on a ticketing system. This project deploys **osTicket** —
a widely-used open-source ITSM platform — integrates it with Active Directory for
single sign-on, and simulates a real Helpdesk environment using incidents generated
from the existing lab infrastructure.

**What this project demonstrates:**
- Deploying and configuring a production-grade ITSM platform from scratch
- Integrating a web application with Active Directory via LDAP
- Managing incident lifecycles with defined SLA policies
- Documenting real P1, P2, and P3 tickets from actual lab incidents
- Building escalation workflows and Helpdesk operational runbooks

---

## 🖥️ Environment

<table>
<tr>
<td width="50%" valign="top">

**Infrastructure**
| Component | Details |
|-----------|---------|
| **ITSM Platform** | osTicket on Ubuntu 25 |
| **Host IP** | `192.168.1.xx` |
| **Web URL** | `http://192.168.1.xx/osticket` |
| **Database** | MariaDB |
| **Web Server** | Apache2 + PHP |
| **AD Domain** | `InfoTech.com` |
| **Primary DC** | `VM-WINSERV-01` — `192.168.1.10` |

</td>
<td width="50%" valign="top">

**Stack**
| Component | Tool |
|-----------|------|
| **ITSM** | osTicket 1.18.x |
| **Web Server** | Apache2 |
| **Language** | PHP 8.x |
| **Database** | MariaDB 10.x |
| **Auth** | LDAP → Active Directory |
| **Host OS** | Ubuntu 25 Desktop |
| **Virtualisation** | VMware Workstation Pro |

</td>
</tr>
</table>

---

## 🏗️ Architecture

```
┌──────────────────────────────────────────────────┐
│           Ubuntu 25 Host (192.168.1.xx)          │
│                                                  │
│   ┌──────────────────────────────────────────┐   │
│   │               osTicket                   │   │
│   │          Apache2 + PHP + MariaDB         │   │
│   │                                          │   │
│   │  Port 80  → Web UI (HTTP)               │   │
│   │  LDAP 389 → AD Authentication           │   │
│   └──────────────────────────────────────────┘   │
└──────────────────────┬───────────────────────────┘
                       │ LDAP authentication
                       ↕
┌──────────────────────────────────────────────────┐
│       VM-DEV-WINSERV-01 (192.168.1.10)          │
│       Active Directory — InfoTech.com            │
│                                                  │
│  Users: Paula Doe, Dave Doe, Ram Doe,           │
│         Sue, wazuhtest, helpdesk staff           │
└──────────────────────────────────────────────────┘

Browser access from any machine on 192.168.1.0/24:
  http://192.168.1.xx/osticket
  Login with InfoTech.com AD credentials
```

---

## 📁 Repository Structure

```
itsm-helpdesk-lab/
│
├── tickets/
│   ├── P1-replication-failure.md       # P1 ticket — AD replication error 8524    ⏳
│   ├── P2-account-lockout.md           # P2 ticket — Administrator lockout         ⏳
│   ├── P2-brute-force-detection.md     # P2 ticket — Wazuh brute force alert      ⏳
│   └── P3-new-user-onboarding.md       # P3 ticket — New user onboarding request  ⏳
│
├── workflows/
│   ├── sla-policy.md                   # P1/P2/P3 SLA definitions                 ⏳
│   ├── p1-incident-response.md         # P1 escalation workflow                    ⏳
│   └── p2-incident-response.md         # P2 incident workflow                      ⏳
│
├── config/
│   ├── osticket-settings.md            # osTicket configuration reference          ⏳
│   └── ad-integration.md               # AD/LDAP integration steps                ⏳
│
├── docs/
│   └── runbook.md                      # Helpdesk operational runbook              ⏳
│
└── README.md
```

> ⏳ = In progress — added as the project develops

---

## 🧩 Build Progress

| # | Phase | Status |
|---|-------|--------|
| 1 | Install LAMP stack on Ubuntu | ⏳ Pending |
| 2 | Install and configure osTicket | ⏳ Pending |
| 3 | Configure departments, teams, and SLA plans | ⏳ Pending |
| 4 | Integrate osTicket with InfoTech.com AD via LDAP | ⏳ Pending |
| 5 | Create and document real tickets from lab incidents | ⏳ Pending |
| 6 | Write SLA policy and escalation workflows | ⏳ Pending |
| 7 | Write Helpdesk runbook + push to GitHub | ⏳ Pending |

---

## 🎯 Tickets to Be Documented

| Priority | Title | Source Lab | Status |
|----------|-------|-----------|--------|
| P1 | AD Replication Failure — Error 8524 | AD & Windows Server Labs | ⏳ |
| P2 | Administrator Account Locked Out — Both DCs | Wazuh SIEM Lab | ⏳ |
| P2 | Brute Force Alert — wazuhtest Account | Wazuh SIEM Lab | ⏳ |
| P3 | New User Onboarding — Jane Smith (IT Dept) | AD Automation Toolkit | ⏳ |

---

## 📋 SLA Policy Overview

| Priority | Description | First Response | Resolution |
|----------|-------------|---------------|------------|
| **P1** | Critical — service down, full impact | 15 minutes | 4 hours |
| **P2** | High — service degraded, significant impact | 1 hour | 8 hours |
| **P3** | Medium — single user affected, workaround available | 4 hours | 24 hours |
| **P4** | Low — general requests, no immediate impact | 8 hours | 72 hours |

---

<div align="center">
<sub>🎫 Built for learning • ⭐ Star if you find this useful • More phases coming soon</sub>
</div>