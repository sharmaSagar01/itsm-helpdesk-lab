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
│   ├── sla-policy.md                   # P1/P2/P3 SLA definitions                 ✅
│   ├── p1-incident-response.md         # P1 escalation workflow                    ⏳
│   └── p2-incident-response.md         # P2 incident workflow                      ⏳
│
├── config/
│   ├── osticket-settings.md            # osTicket configuration reference          ✅
│   └── ad-integration.md               # AD/LDAP integration steps                ✅
│
├── docs/
│   └── runbook.md                      # Helpdesk operational runbook              ⏳
│
└── README.md
```

> ⏳ = In progress — added as the project develops

---

## 🧩 Build Progress

| #   | Phase                                               | Status       |
| --- | --------------------------------------------------- | ------------ |
| 1   | Install LAMP stack on Ubuntu                        | ✅ Completed |
| 2   | Install and configure osTicket                      | ✅ Completed |
| 3   | Configure departments, teams, and SLA plans         | ✅ Completed |
| 4   | Integrate osTicket with InfoTech.com AD via LDAP    | ⏳ Pending   |
| 5   | Create and document real tickets from lab incidents | ⏳ Pending   |
| 6   | Write SLA policy and escalation workflows           | ⏳ Pending   |
| 7   | Write Helpdesk runbook + push to GitHub             | ⏳ Pending   |

---

## 🎯 Tickets to Be Documented

| Priority | Title                                       | Source Lab               | Status |
| -------- | ------------------------------------------- | ------------------------ | ------ |
| P1       | AD Replication Failure — Error 8524         | AD & Windows Server Labs | ⏳     |
| P2       | Administrator Account Locked Out — Both DCs | Wazuh SIEM Lab           | ⏳     |
| P2       | Brute Force Alert — wazuhtest Account       | Wazuh SIEM Lab           | ⏳     |
| P3       | New User Onboarding — Jane Smith (IT Dept)  | AD Automation Toolkit    | ⏳     |

---

## 📋 SLA Policy Overview

| Priority | Description                                         | First Response | Resolution |
| -------- | --------------------------------------------------- | -------------- | ---------- |
| **P1**   | Critical — service down, full impact                | 15 minutes     | 4 hours    |
| **P2**   | High — service degraded, significant impact         | 1 hour         | 8 hours    |
| **P3**   | Medium — single user affected, workaround available | 4 hours        | 24 hours   |
| **P4**   | Low — general requests, no immediate impact         | 8 hours        | 72 hours   |

---

# ✅ Phase 1 — Install the LAMP Stack

## 📋 What This Phase Covers

osTicket runs on a **LAMP stack** — Linux, Apache, MySQL/MariaDB, and PHP.
This phase installs and configures all four components on the Ubuntu host
alongside the existing Wazuh SIEM stack.

```bash

sudo ss -tlnp | grep ":80\|:443\|:3306"

```

**Port check confirmed before starting:**

| Port   | Status    | Used By           |
| ------ | --------- | ----------------- |
| `443`  | 🔴 In use | Wazuh Dashboard   |
| `80`   | 🟢 Free   | osTicket (Apache) |
| `3306` | 🟢 Free   | MariaDB           |

osTicket runs on port `80` — no conflict with Wazuh on port `443`.

---

## 🚀 Installation Steps

**Step 1 — Update Ubuntu**

```bash
sudo apt update && sudo apt upgrade -y
```

**Step 2 — Install Apache**

```bash
sudo apt install apache2 -y
sudo systemctl start apache2
sudo systemctl enable apache2
sudo systemctl status apache2
```

**Step 3 — Install MariaDB**

```bash
sudo apt install mariadb-server mariadb-client -y
sudo systemctl start mariadb
sudo systemctl enable mariadb

# Secure the installation
sudo mysql_secure_installation
```

When prompted during `mysql_secure_installation`:

| Prompt                               | Answer                  |
| ------------------------------------ | ----------------------- |
| Enter current root password          | Press Enter (none yet)  |
| Switch to unix_socket authentication | N                       |
| Change the root password             | Y → set strong password |
| Remove anonymous users               | Y                       |
| Disallow root login remotely         | Y                       |
| Remove test database                 | Y                       |
| Reload privilege tables              | Y                       |

**Step 4 — Install PHP and Required Extensions**

```bash
sudo apt install php php-mysqli php-gd php-xml php-mbstring \
    php-intl php-apcu php-imap php-curl php-zip \
    libapache2-mod-php -y

# Verify
php --version
```

**Step 5 — Create the osTicket Database**

```bash
sudo mysql -u root -p
```

Inside MariaDB:

```sql
CREATE DATABASE osticket;
CREATE USER 'osticket'@'localhost' IDENTIFIED BY 'osTicket@12345!';
GRANT ALL PRIVILEGES ON osticket.* TO 'osticket'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

**Step 6 — Verify All Services Running**

```bash
# Confirm Apache and MariaDB are listening
sudo ss -tlnp | grep ":80\|:3306"

# Confirm PHP is installed
php --version

# Confirm Apache is active
sudo systemctl status apache2 --no-pager

# Confirm MariaDB is active
sudo systemctl status mariadb --no-pager
```

---

## ✅ Outcome

- Ubuntu packages updated ✅
- Apache2 installed and running on port `80` ✅
- MariaDB installed, secured, and running on port `3306` ✅
- PHP 8.x installed with all required extensions ✅
- `osticket` database and user created in MariaDB ✅
- Both services enabled for auto-start on boot ✅

---

## 📸 Screenshots

<p align="center">
  <img src="Screenshots/phase1-img1.png" width="45%" />
  <img src="Screenshots/phase1-img2.png" width="45%" />
</p>
<p align="center">
  <img src="Screenshots/phase1-img3.png" width="45%" />
  </p>

---

# ✅ Phase 2 — Install and Configure osTicket

## 📋 What This Phase Covers

With the LAMP stack running, this phase downloads osTicket, deploys it
under Apache, runs the web-based installer, and performs the essential
post-install configuration — departments, roles, teams, and help topics —
to make it ready for real ticket creation.

---

## 🚀 Installation Steps

### Part A — Download and Deploy osTicket

**Step 1 — Download the latest osTicket release**

```bash
cd /tmp
wget https://github.com/osTicket/osTicket/releases/download/v1.18.1/osTicket-v1.18.1.zip
```

**Step 2 — Extract and move to Apache web root**

```bash
sudo apt install unzip -y
unzip osTicket-v1.18.1.zip -d osTicket
sudo mv osTicket/upload /var/www/html/osticket
```

**Step 3 — Set correct permissions**

```bash
sudo chown -R www-data:www-data /var/www/html/osticket
sudo chmod -R 755 /var/www/html/osticket
```

**Step 4 — Copy the sample config file**

```bash
sudo cp /var/www/html/osticket/include/ost-sampleconfig.php \
        /var/www/html/osticket/include/ost-config.php

sudo chmod 0666 /var/www/html/osticket/include/ost-config.php
```

**Step 5 — Enable Apache rewrite module and restart**

```bash
sudo a2enmod rewrite
sudo systemctl restart apache2
```

---

### Part B — Run the Web Installer

Open a browser on any machine on the `192.168.1.0/24` network:

```
http://192.168.1.xx/osticket/setup
```

Work through the installer screens:

**Screen 1 — Prerequisites check**
All items should show green ✅. If any PHP extension shows red — install it:

```bash
sudo apt install php-<extension-name> -y
sudo systemctl restart apache2
```

**Screen 2 — Basic Configuration**

| Field         | Value                  |
| ------------- | ---------------------- |
| Helpdesk Name | `InfoTech IT Support`  |
| Default Email | `support@infotech.com` |

**Screen 3 — Admin Account**

| Field      | Value                             |
| ---------- | --------------------------------- |
| First Name | `Admin`                           |
| Last Name  | `User`                            |
| Email      | `admin@infotech.com`              |
| Username   | `admin`                           |
| Password   | Set a strong password and save it |

**Screen 4 — Database Settings**

| Field              | Value             |
| ------------------ | ----------------- |
| MySQL Table Prefix | `ost_`            |
| MySQL Hostname     | `localhost`       |
| MySQL Database     | `osticket`        |
| MySQL Username     | `osticket`        |
| MySQL Password     | `osTicket@12345!` |

Click **Install Now** → wait for the installation to complete.

---

### Part C — Post-Install Cleanup

```bash
# Remove the setup directory — required for security
sudo rm -rf /var/www/html/osticket/setup

# Lock down the config file
sudo chmod 0644 /var/www/html/osticket/include/ost-config.php
```

---

### Part D — Post-Install Configuration (Admin Panel)

Navigate to the Admin Panel:

```
http://192.168.1.xx/osticket/scp
```

Log in with `admin` credentials set during installation.

**Configure Departments:**

Go to **Admin Panel → Agents → Departments → Add New Department**

| Department       | Type    | Purpose                    |
| ---------------- | ------- | -------------------------- |
| `IT Support`     | Public  | General helpdesk tickets   |
| `Infrastructure` | Private | Server and AD issues       |
| `Security`       | Private | Wazuh alerts and incidents |

**Configure Teams:**

Go to **Admin Panel → Agents → Teams → Add New Team**

| Team               | Purpose                                         |
| ------------------ | ----------------------------------------------- |
| `Level I Support`  | First response — password resets, access issues |
| `Level II Support` | Escalated issues — server, AD, network          |

**Configure Roles:**

Go to **Admin Panel → Agents → Roles → Add New Role**

| Role             | Permissions                          |
| ---------------- | ------------------------------------ |
| `Helpdesk Agent` | Create, reply, close tickets         |
| `Senior Agent`   | All above + assign, transfer, delete |
| `Administrator`  | Full access                          |

**Configure Help Topics:**

Go to **Admin Panel → Manage → Help Topics → Add New Help Topic**

| Help Topic            | Department     | Priority  |
| --------------------- | -------------- | --------- |
| `Account Locked Out`  | IT Support     | High      |
| `New User Onboarding` | IT Support     | Normal    |
| `Security Alert`      | Security       | Emergency |
| `Server / AD Issue`   | Infrastructure | High      |
| `Password Reset`      | IT Support     | Normal    |
| `General IT Request`  | IT Support     | Low       |

---

### Part E — Configure Email Settings

Go to **Admin Panel → Emails → Emails → Add New Email**

| Field         | Value                  |
| ------------- | ---------------------- |
| Email Address | `support@infotech.com` |
| Name          | `InfoTech IT Support`  |
| Department    | `IT Support`           |

This is the address users see when they receive ticket notifications.

---

## ✅ Outcome

- osTicket downloaded and deployed to `/var/www/html/osticket` ✅
- Web installer completed successfully ✅
- Setup directory removed — installation secured ✅
- Admin panel accessible at `http://192.168.1.xx/osticket/scp` ✅
- Departments created: IT Support, Infrastructure, Security ✅
- Teams created: Level I and Level II Support ✅
- Help topics configured for all common ticket types ✅

---

## 📸 Screenshots

<p align="center">
  <img src="Screenshots/phase2-img1.png" width="45%" />
  <img src="Screenshots/phase2-img2.png" width="45%" />
</p>

---

# ✅ Phase 3 — Configure Departments, Teams, SLA Plans & Agents

## 📋 What This Phase Covers

With osTicket installed, this phase builds the operational structure —
SLA plans, agents, and ticket routing — so the system behaves like a
real Helpdesk environment. Every setting configured here directly maps
to the incidents documented in the `tickets/` and `workflows/` folders.

> Full configuration reference: [`config/osticket-settings.md`](config/osticket-settings.md)
> Full SLA policy: [`workflows/sla-policy.md`](workflows/sla-policy.md)

---

## ⚙️ Part A — Configure SLA Plans

Navigate to: **Admin Panel → Manage → SLA Plans → Add New SLA Plan**

Create all four plans:

| SLA Plan | Grace Period | Schedule       | Used For                      |
| -------- | ------------ | -------------- | ----------------------------- |
| `SEV-1`  | 1 hour       | 24/7           | Critical — service down       |
| `SEV-2`  | 4 hours      | 24/7           | High — significant impact     |
| `SEV-3`  | 8 hours      | Business hours | Medium — single user affected |
| `SEV-4`  | 72 hours     | Business hours | Low — general requests        |

**How to create each one:**

- Click **Add New SLA Plan**
- Set the name (e.g. `SEV-1`)
- Set Grace Period in hours
- Set Schedule (`24/7` or `Monday-Friday 8am-5pm`)
- Enable → Save

---

## ⚙️ Part B — Configure Agents

Navigate to: **Admin Panel → Agents → Add New Agent**

Create agents using your existing AD lab users:

| Name      | Email                  | Username  | Department | Role           | Team             |
| --------- | ---------------------- | --------- | ---------- | -------------- | ---------------- |
| Paula Doe | paula@infotech.com | paula | IT Support | Senior Agent   | Level II Support |
| Dave Doe  | dave@infotech.com  | dave  | IT Support | Helpdesk Agent | Level I Support  |
| Sue       | sue@infotech.com       | sue       | Security   | Senior Agent   | Level II Support |

**For each agent:**

- Fill in name and email
- Set username matching their AD account
- Assign department and role
- Assign to team
- Set a temporary password — they will reset on first login

---

## ⚙️ Part C — Configure Users (Ticket Submitters)

Navigate to: **Agent Panel → Users → Add User**

These are the end users who submit tickets — also mapped to your AD lab:

| Name          | Email                 |
| ------------- | --------------------- |
| Ram Doe       | rdoe@infotech.com  |
| Jessy Merch      | jmerch@infotech.com |
| Alice Johnson | ajohnson@infotech.com |

---

## ⚙️ Part D — Link Help Topics to Departments and SLA Plans

Navigate to: **Admin Panel → Manage → Help Topics**

Update each help topic with the correct department and SLA:

| Help Topic            | Department     | SLA   | Priority  |
| --------------------- | -------------- | ----- | --------- |
| `Account Locked Out`  | IT Support     | SEV-2 | High      |
| `Security Alert`      | Security       | SEV-1 | Emergency |
| `Server / AD Issue`   | Infrastructure | SEV-1 | High      |
| `New User Onboarding` | IT Support     | SEV-3 | Normal    |
| `Password Reset`      | IT Support     | SEV-3 | Normal    |
| `General IT Request`  | IT Support     | SEV-4 | Low       |

---

## ⚙️ Part E — Configure Ticket Settings

Navigate to: **Admin Panel → Settings → Tickets**

| Setting              | Value                | Reason                                    |
| -------------------- | -------------------- | ----------------------------------------- |
| Default SLA          | `SEV-3`              | Safe default for unclassified tickets     |
| Default Priority     | `Normal`             | Agents can escalate as needed             |
| Allow HTML           | `Yes`                | Cleaner ticket formatting                 |
| Ticket Number Format | `#%YYYY%MM%DD-%####` | Date-based numbering                      |
| Auto-assign tickets  | `Yes`                | Route to correct department automatically |

---

## ✅ Outcome

- SLA plans SEV-1 through SEV-4 configured with correct grace periods ✅
- Agents created — Paula, Dave, Sue — matching existing AD lab users ✅
- End users created — Ram, John, Alice — matching onboarded AD accounts ✅
- Help topics linked to correct departments and SLA plans ✅
- Ticket settings configured — auto-assign and HTML enabled ✅

---

## 📸 Screenshots

<p align="center">
  <img src="Screenshots/phase3-img1.png" width="45%" />
  <img src="Screenshots/phase3-img2.png" width="45%" />
</p>
<p align="center">
  <img src="Screenshots/phase3-img3.png" width="45%" />
  </p>
