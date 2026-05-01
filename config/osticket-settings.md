# ⚙️ osTicket Configuration Reference

> Complete configuration reference for the `itsm-helpdesk-lab` osTicket instance.
> Documents every setting across all phases for full reproducibility.

---

## 🌐 Access

| Item                 | Value                              |
| -------------------- | ---------------------------------- |
| **Admin Panel**      | `http://192.168.1.xx/osticket/scp` |
| **User Portal**      | `http://192.168.1.xx/osticket`     |
| **Admin Username**   | `itadmin`                          |
| **Helpdesk Name**    | `InfoTech IT Support`              |
| **Default Email**    | `support@infotech.com`             |
| **osTicket Version** | `1.18.1`                           |

---

## 🗂️ Departments

| Department       | Type    | Default SLA | Purpose                                   |
| ---------------- | ------- | ----------- | ----------------------------------------- |
| `IT Support`     | Public  | SEV-3       | General helpdesk — first point of contact |
| `Infrastructure` | Private | SEV-1       | Server, AD, and network issues            |
| `Security`       | Private | SEV-1       | Wazuh alerts and security incidents       |

---

## 👥 Teams

| Team               | Members        | Handles                                                       |
| ------------------ | -------------- | ------------------------------------------------------------- |
| `Level I Support`  | Dave Doe       | First response — account issues, onboarding, general requests |
| `Level II Support` | Paula Doe, Sue | Escalated — AD, servers, security alerts                      |

---

## 🔑 Roles

| Role             | Permissions                          |
| ---------------- | ------------------------------------ |
| `Helpdesk Agent` | Create, reply, close tickets         |
| `Senior Agent`   | All above + assign, transfer, delete |
| `Administrator`  | Full system access                   |

---

## 👤 Agents

| Name       | Username  | Department                  | Role           | Team     |
| ---------- | --------- | --------------------------- | -------------- | -------- |
| Admin User | `itadmin` | —                           | Administrator  | —        |
| Paula Doe  | `paula`   | IT Support / Infrastructure | Senior Agent   | Level II |
| Dave Doe   | `dave`    | IT Support                  | Helpdesk Agent | Level I  |
| Sue        | `sue`     | Security                    | Senior Agent   | Level II |

---

## 👥 Users (Ticket Submitters)

| Name          | Email                 |
| ------------- | --------------------- |
| Ram Doe       | ram.doe@infotech.com  |
| Jessy Merch   | jmerch@infotech.com |
| Alice Johnson | ajohnson@infotech.com |

---

## 📋 SLA Plans

| Plan    | Grace Period | Schedule       |
| ------- | ------------ | -------------- |
| `SEV-1` | 1 hour       | 24/7           |
| `SEV-2` | 4 hours      | 24/7           |
| `SEV-3` | 8 hours      | Business Hours |
| `SEV-4` | 72 hours     | Business Hours |

> Full details: [`workflows/sla-policy.md`](../workflows/sla-policy.md)

---

## 🏷️ Help Topics

| Help Topic            | Department     | SLA   | Priority  |
| --------------------- | -------------- | ----- | --------- |
| `Security Alert`      | Security       | SEV-1 | Emergency |
| `Server / AD Issue`   | Infrastructure | SEV-1 | High      |
| `Account Locked Out`  | IT Support     | SEV-2 | High      |
| `New User Onboarding` | IT Support     | SEV-3 | Normal    |
| `Password Reset`      | IT Support     | SEV-3 | Normal    |
| `General IT Request`  | IT Support     | SEV-4 | Low       |

---

## 🔧 Ticket Settings

| Setting                      | Value                |
| ---------------------------- | -------------------- |
| Default SLA                  | SEV-3                |
| Default Priority             | Normal               |
| Ticket Number Format         | `#%YYYY%MM%DD-%####` |
| Claim on Response            | Enabled              |
| Human Verification (CAPTCHA) | Disabled             |
| Default Ticket Queue         | Open                 |

---

## 🔒 Authentication

| Setting         | Value                                               |
| --------------- | --------------------------------------------------- |
| Backend         | osTicket local authentication                       |
| LDAP Attempted  | Yes — incompatibility with `Net_LDAP2` on Ubuntu 26 |
| Agent usernames | Match AD identities for portfolio consistency       |

> Full LDAP attempt details: [`ad-integration.md`](ad-integration.md)

---

## 📦 Server

| Component            | Details                                         |
| -------------------- | ----------------------------------------------- |
| **OS**               | Ubuntu 25                                       |
| **Web Server**       | Apache2                                         |
| **PHP**              | 8.x                                             |
| **Database**         | MariaDB 10.x                                    |
| **DB Name**          | `osticket`                                      |
| **DB User**          | `osticket`                                      |
| **Install Path**     | `/var/www/html/osticket`                        |
| **Config File**      | `/var/www/html/osticket/include/ost-config.php` |
| **Apache Error Log** | `/var/log/apache2/error.log`                    |

---

<div align="center">
<sub>⚙️ osTicket Configuration Reference | InfoTech IT Support | itsm-helpdesk-lab</sub>
</div>
