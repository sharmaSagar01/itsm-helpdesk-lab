# 🟠 P2 Incident Response Workflow

> SEV-2 — High severity incidents affecting specific users, security events, or degraded services.
> Grace period: **4 hours** | Schedule: **24/7** | Resolution target: **8 hours**

---

## ⚡ First Response (T+0 to T+60 minutes)

```
1. Ticket created — SEV-2 SLA timer starts (4-hour grace period)
2. Assign to Level I Support (Dave Doe) for initial triage
3. Send acknowledgement to requester within 1 hour:

   "We have received your request and our team is investigating.
    We will provide a full update within 2 hours."

4. Begin triage — gather full details if the description is incomplete
5. Confirm: is this genuinely P2, or should it be escalated to P1?
   If domain-wide or security breach → escalate to P1 immediately
```

---

## 🔍 Triage (T+1 to T+2 hours)

```
6. Can Level I resolve this?
   YES → proceed to resolution
   NO  → escalate to Level II at T+2 hours maximum

Escalation message (internal note in the ticket):
   "Escalating to Level II — unable to resolve [issue] at Level I.
    Steps already taken: [list what was tried].
    User last updated at [time]."

7. Level II (Paula Doe or Sue) takes ownership
8. Reviews Level I notes — no need to repeat diagnosis from scratch
9. Updates requester every hour while investigating
```

---

## 🔧 Resolution (T+2 to T+8 hours)

```
10. Apply the fix — document every command and step in the ticket thread
11. Verify the fix from the affected user's perspective
12. Send resolution reply:

    "Your issue has been resolved. [Brief summary of what was done].
     Please confirm everything is working on your side and let us know
     if you need any further assistance."

13. Set ticket status to Resolved
14. Add resolution category and root cause for reporting
```

---

## ⚠️ Escalation Triggers

| Time | Trigger | Action |
|------|---------|--------|
| T+1hr | First response not sent | Warning — send immediately |
| T+2hrs | Level I cannot resolve | Escalate to Level II |
| T+4hrs | SLA grace period expires | Ticket flagged overdue — notify manager |
| T+8hrs | Hard resolution target | If missed → document reason, review required |

---

## 🔒 Security Alert P2 — Additional Steps

When a P2 is triggered by a Wazuh security alert:

```
a. Assign directly to Security (Sue) — do NOT route through Level I
b. Identify source IP and affected account immediately
c. Determine intent:
   - Internal test account → resolve, document, confirm rules working
   - Misconfiguration → fix and document
   - Genuine external threat → escalate to P1 immediately
d. Update Wazuh rules or audit policy if alert reveals a gap
e. Document findings in ticket with Wazuh rule IDs and Event IDs
```

---

## 🔑 Account Lockout P2 — Procedure

```
a. Identify the locked account from the ticket description
b. Verify it is locked:

   Get-ADUser -Identity "username" -Properties LockedOut | Select Name, LockedOut

c. Check the source of the failed logins (Event ID 4625):

   Get-WinEvent -FilterHashtable @{ LogName='Security'; Id=4625 } -MaxEvents 10 |
       Select TimeCreated,
       @{N="User";   E={$_.Properties[5].Value}},
       @{N="Source"; E={$_.Properties[19].Value}}

d. If source is legitimate → unlock and notify user
e. If source is suspicious → unlock but also block source IP

   Unlock-ADAccount -Identity "username"

f. Reset password if requested:

   Set-ADAccountPassword -Identity "username" -Reset `
       -NewPassword (ConvertTo-SecureString "Temp@12345!" -AsPlainText -Force)
   Set-ADUser -Identity "username" -ChangePasswordAtLogon $true
```

---

## 📋 P2 Examples from This Lab

| Ticket | Issue | Assigned To | Resolution Time |
|--------|-------|-------------|----------------|
| `#2026041402` | Administrator Account Locked Out — Both DCs | Paula Doe | 1h 30min ✅ |
| `#2026041403` | Wazuh Brute Force Alert — wazuhtest | Sue | 1h 05min ✅ |

Both resolved well within the 8-hour SEV-2 resolution target.

---

<div align="center">
<sub>🟠 P2 Incident Response Workflow | InfoTech IT Support | itsm-helpdesk-lab</sub>
</div>