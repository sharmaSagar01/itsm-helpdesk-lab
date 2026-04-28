# ⚙️ osTicket Configuration Reference

> Full configuration reference for the `itsm-helpdesk-lab` osTicket instance.
> Documents every setting configured across all phases for reproducibility.

---

## 🌐 Access Details

| Item | Value |
|------|-------|
| **Admin Panel** | `http://192.168.1.xx/osticket/scp` |
| **User Portal** | `http://192.168.1.xx/osticket` |
| **Admin Username** | `itadmin` |
| **Helpdesk Name** | `InfoTech IT Support` |
| **Default Email** | `support@infotech.com` |

---

## 🗂️ Departments

| Department | Type | SLA | Purpose |
|------------|------|-----|---------|
| `IT Support` | Public | SEV-3 | General helpdesk — first point of contact |
| `Infrastructure` | Private | SEV-1 | Server, AD, network issues |
| `Security` | Private | SEV-1 | Wazuh alerts, security incidents |

---

## 👥 Teams

| Team | Members | Handles |
|------|---------|---------|
| `Level I Support` | Dave Doe | First response — account issues, password resets |
| `Level II Support` | Paula Doe, Sue | Escalated issues — AD, servers, security alerts |

---

## 🔑 Roles

| Role | Permissions |
|------|------------|
| `Helpdesk Agent` | Create, reply, close tickets |
| `Senior Agent` | All above + assign, transfer, delete |
| `Administrator` | Full system access |

---

## 👤 Agents

| Name | Username | Department | Role | Team |
|------|---------|------------|------|------|
| Admin User | `itadmin` | — | Administrator | — |
| Paula Doe | `paula` | IT Support | Senior Agent | Level II Support |
| Dave Doe | `dave` | IT Support | Helpdesk Agent | Level I Support |
| Sue | `sue` | Security | Senior Agent | Level II Support |

---

## 👥 Users (Ticket Submitters)

| Name | Email |
|------|-------|
| Ram Doe | rdoe@infotech.com |
| Jessy Merch | jmerch@infotech.com |
| Alice Johnson | ajohnson@infotech.com |

---

## 📋 SLA Plans

| Plan | Grace Period | Schedule |
|------|-------------|---------|
| `SEV-1` | 1 hour | 24/7 |
| `SEV-2` | 4 hours | 24/7 |
| `SEV-3` | 8 hours | Business Hours |
| `SEV-4` | 72 hours | Business Hours |

> Full SLA policy details: [`workflows/sla-policy.md`](../workflows/sla-policy.md)

---

## 🏷️ Help Topics

| Help Topic | Department | SLA | Priority |
|------------|------------|-----|---------|
| `Account Locked Out` | IT Support | SEV-2 | High |
| `Security Alert` | Security | SEV-1 | Emergency |
| `Server / AD Issue` | Infrastructure | SEV-1 | High |
| `New User Onboarding` | IT Support | SEV-3 | Normal |
| `Password Reset` | IT Support | SEV-3 | Normal |
| `General IT Request` | IT Support | SEV-4 | Low |

---

## 🔧 Ticket Settings

| Setting | Value |
|---------|-------|
| Default SLA | SEV-3 |
| Default Priority | Normal |
| Allow HTML | Yes |
| Ticket Number Format | `#%YYYY%MM%DD-%####` |
| Auto-assign tickets | Yes |
| Claim on Response | Yes |

---

## 🔒 LDAP / AD Integration

> Full AD integration steps: [`ad-integration.md`](ad-integration.md)

| Setting | Value |
|---------|-------|
| Authentication Backend | LDAP |
| LDAP Server | `192.168.1.10` (VM-WINSERV-01) |
| LDAP Port | `389` |
| Base DN | `DC=InfoTech,DC=com` |
| Bind DN | `CN=Administrator,CN=Users,DC=InfoTech,DC=com` |
| Search Filter | `(sAMAccountName=%s)` |
| Status | ⏳ Configured in Phase 4 |

---

## 📦 Server Details

| Component | Details |
|-----------|---------|
| **OS** | Ubuntu 25 |
| **Web Server** | Apache2 |
| **PHP Version** | 8.x |
| **Database** | MariaDB 10.x |
| **Database Name** | `osticket` |
| **DB User** | `osticket` |
| **Install Path** | `/var/www/html/osticket` |
| **Config File** | `/var/www/html/osticket/include/ost-config.php` |

---

<div align="center">
<sub>⚙️ osTicket Configuration Reference | InfoTech IT Support | itsm-helpdesk-lab</sub>
</div>