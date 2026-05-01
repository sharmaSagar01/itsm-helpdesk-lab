# 📖 InfoTech Helpdesk — Operational Runbook

> Day-to-day operational guide for the `itsm-helpdesk-lab` osTicket instance.
> Covers daily checks, ticket handling, escalation, and common procedures.

---

## 📑 Table of Contents

| #   | Section                                                   |
| --- | --------------------------------------------------------- |
| 1   | [Environment Reference](#environment-reference)           |
| 2   | [Daily Operations Checklist](#daily-operations-checklist) |
| 3   | [Handling Incoming Tickets](#handling-incoming-tickets)   |
| 4   | [SLA Quick Reference](#sla-quick-reference)               |
| 5   | [Common Procedures](#common-procedures)                   |
| 6   | [Escalation Guide](#escalation-guide)                     |
| 7   | [Troubleshooting osTicket](#troubleshooting-osticket)     |
| 8   | [Quick Reference Commands](#quick-reference-commands)     |

---

## Environment Reference

| Item                     | Value                              |
| ------------------------ | ---------------------------------- |
| **osTicket Admin Panel** | `http://192.168.1.xx/osticket/scp` |
| **User Portal**          | `http://192.168.1.19/osticket`     |
| **Admin Username**       | `itadmin`                          |
| **Primary DC**           | `VM-WINSERV-01` — `192.168.1.10`   |
| **Secondary DC**         | `VM-WINSERV-02` — `192.168.1.12`   |
| **Domain**               | `InfoTech.com`                     |
| **Wazuh Dashboard**      | `https://192.168.1.xx`             |

### Agents

| Agent     | Username    | Department                  | Handles                                        |
| --------- | ----------- | --------------------------- | ---------------------------------------------- |
| Paula Doe | `paula` | IT Support / Infrastructure | Senior — AD, server issues, escalations        |
| Dave Doe  | `dave`  | IT Support                  | Level I — password resets, onboarding, general |
| Sue       | `sue`       | Security                    | Security alerts, Wazuh investigations          |

---

## Daily Operations Checklist

Run every morning before the shift starts:

```
☐ Log into osTicket Admin Panel
☐ Check Open Tickets — any P1 or P2 from overnight?
☐ Review SLA status — any tickets flagged overdue?
☐ Check Wazuh dashboard for overnight alerts
☐ Confirm both Windows Servers are reachable
☐ Confirm AD replication is healthy
```

**osTicket quick checks:**

```
Agent Panel → Tickets → Open       ← Review all open tickets
Agent Panel → Tickets → Overdue    ← Immediate action required
```

**AD health check:**

```powershell
# Run on VM-WINSERV-01
repadmin /replsummary
Get-ADUser -Filter {LockedOut -eq $true}
```

---

## Handling Incoming Tickets

### Step 1 — Triage

```
1. Read the full description
2. Confirm correct Help Topic and SLA are set
3. Assign to correct agent:
   AD / server issues  → Paula Doe (Infrastructure)
   Security alerts     → Sue (Security)
   General IT          → Dave Doe (IT Support)
4. Set priority if incorrect
5. Send first response within SLA window
```

### Step 2 — Investigate

```
6. Gather missing information from the user
7. Document every diagnostic step as an Internal Note
8. Do not close until fix is verified from user's side
```

### Step 3 — Resolve and Close

```
9.  Apply the fix
10. Verify with the user — send a reply asking to confirm
11. Once confirmed → set Status to Resolved
12. Add resolution note summarising what was done and why
13. Set Resolution Category for reporting
```

---

## SLA Quick Reference

| Plan  | Grace Period | Schedule       | Action at Expiry                     |
| ----- | ------------ | -------------- | ------------------------------------ |
| SEV-1 | 1 hour       | 24/7           | Immediate escalation to itadmin      |
| SEV-2 | 4 hours      | 24/7           | Escalate to Level II if not resolved |
| SEV-3 | 8 hours      | Business hours | Flag for senior review               |
| SEV-4 | 72 hours     | Business hours | Review in weekly ticket audit        |

---

## Common Procedures

### Unlock a User Account

```powershell
Unlock-ADAccount -Identity "username"
Get-ADUser -Identity "username" -Properties LockedOut | Select Name, LockedOut
```

### Reset a User Password

```powershell
Set-ADAccountPassword -Identity "username" `
    -NewPassword (ConvertTo-SecureString "TempPass@123!" -AsPlainText -Force) -Reset
Set-ADUser -Identity "username" -ChangePasswordAtLogon $true
```

### Onboard a New User

```powershell
.\scripts\New-UserOnboard.ps1 `
    -FirstName "First" -LastName "Last" `
    -Department "IT" -JobTitle "Job Title" -Manager "manager.name"
```

### Check AD Replication

```powershell
repadmin /replsummary          # Quick — 0 failures = healthy
repadmin /showrepl             # Detailed per-partition
repadmin /syncall /AdeP        # Force full sync
```

### Check Wazuh for Security Events

```
https://192.168.1.19 → Threat Hunting → search by rule.id or Event ID
```

| Search                          | Finds                    |
| ------------------------------- | ------------------------ |
| `rule.id: 100001`               | Account lockout alerts   |
| `rule.id: 100102`               | Brute force alerts       |
| `data.win.system.eventID: 4625` | Failed logins            |
| `data.win.system.eventID: 4728` | Group membership changes |

---

## Escalation Guide

| Situation                         | Action                                    |
| --------------------------------- | ----------------------------------------- |
| P1 not resolved in 30 min         | Notify itadmin immediately                |
| P2 Level I cannot resolve in 2hrs | Escalate to Level II                      |
| Security alert from Wazuh         | Assign to Sue — bypass Level I            |
| AD replication failure            | Assign to Paula — P1 priority             |
| Unknown issue type                | Assign to Paula for triage                |
| SLA breached                      | Document reason in ticket, notify manager |

> Full escalation workflows: [`workflows/p1-incident-response.md`](../workflows/p1-incident-response.md) | [`workflows/p2-incident-response.md`](../workflows/p2-incident-response.md)

---

## Troubleshooting osTicket

### osTicket not loading (HTTP 500)

```bash
sudo tail -20 /var/log/apache2/error.log
sudo systemctl restart apache2
sudo chown -R www-data:www-data /var/www/html/osticket/
```

### Can't log into Admin Panel

```bash
sudo systemctl status apache2 mariadb --no-pager
ls /var/www/html/osticket/include/ost-config.php
```

### Database connection error

```bash
sudo systemctl restart mariadb
sudo mysql -u osticket -p osticket   # Test connection
```

---

## Quick Reference Commands

```bash
# Ubuntu / osTicket
sudo systemctl status apache2 mariadb --no-pager
sudo systemctl restart apache2
sudo tail -20 /var/log/apache2/error.log
```

```powershell
# Windows Server (Domain Admin)
Unlock-ADAccount -Identity "username"
repadmin /replsummary
Get-ADUser -Filter {LockedOut -eq $true}
gpupdate /force
.\scripts\New-UserOnboard.ps1 -FirstName "X" -LastName "Y" -Department "IT" -JobTitle "Z"
.\scripts\Get-ADHealthCheck.ps1
```

---

<div align="center">
<sub>📖 InfoTech Helpdesk Runbook | itsm-helpdesk-lab | osTicket 1.18.1</sub>
</div>
