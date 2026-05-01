# 🔴 P1 Incident Response Workflow

> SEV-1 — Critical incidents affecting all users or core infrastructure.
> Grace period: **1 hour** | Schedule: **24/7** | Resolution target: **4 hours**

---

## ⚡ Immediate Actions (T+0 to T+15 minutes)

```
1. Ticket created — SEV-1 SLA timer starts immediately (1-hour grace period)
2. Assign to Level II Support (Paula Doe or Sue) — do NOT leave with Level I
3. Notify itadmin that a P1 is active
4. Send first response to requester within 15 minutes:

   "We have received your P1 report and our senior engineer is
    investigating immediately. We will update you within 30 minutes."

5. Begin diagnosis — do not wait for additional information before starting
```

---

## 🔍 Diagnosis Phase (T+15 to T+60 minutes)

```
6.  Identify the scope — how many users or systems are affected?
7.  Isolate the component — network, AD, server, DNS, application?
8.  Check Wazuh dashboard for any related security alerts
9.  For AD issues — check replication status:

    repadmin /replsummary
    repadmin /showrepl

10. Document all diagnostic steps as Internal Notes in the ticket
11. Update the requester every 30 minutes with status
```

---

## 🔧 Resolution Phase (T+60 minutes onward)

```
12. Apply the fix — document every command run in the ticket thread
13. Verify the fix — confirm service is fully restored
14. Test from the affected user's perspective before closing
15. Send resolution message to requester:

    "The issue has been resolved. [Brief description of what was done].
     Please confirm you can access [service] and let us know if
     the issue persists."

16. Close the ticket — set resolution category and root cause
```

---

## ⚠️ Escalation Triggers

| Time | Trigger | Action |
|------|---------|--------|
| T+15min | First response not sent | Send immediately |
| T+30min | No diagnostic progress | Escalate to itadmin |
| T+60min | SLA grace expires | Ticket flagged overdue — manager notified |
| T+2hrs | Still open | Executive notification |
| T+4hrs | Hard resolution target missed | Post-incident review required |

---

## 📝 Post-Incident Actions

```
17. Write a post-incident summary in the ticket thread:
    - What happened and when
    - Root cause
    - How it was fixed
    - How to prevent recurrence

18. Update runbook or documentation if a gap was revealed
19. Create a follow-up P3 ticket if preventative maintenance is needed
20. If AD-related — run Get-ADHealthCheck.ps1 and confirm all green
```

---

## 🔑 AD Replication P1 — Quick Steps

```powershell
# Step 1 — Diagnose
repadmin /showrepl               # Detailed failure output
repadmin /replsummary            # Fail count summary

# Step 2 — Check connectivity
ping 192.168.1.12                # Confirm network is not the issue

# Step 3 — Check DNS
nslookup <DC2-GUID>._msdcs.InfoTech.com   # CNAME resolution test

# Step 4 — Fix DNS ordering on secondary DC
# Preferred DNS → 192.168.1.10 (primary DC)
# Alternate DNS → 192.168.1.12 (itself)

# Step 5 — Re-register and flush
ipconfig /registerdns
nltest /dsregdns
ipconfig /flushdns

# Step 6 — Disable IPv6 if causing timeouts
Disable-NetAdapterBinding -Name "Ethernet0" -ComponentID ms_tcpip6

# Step 7 — Force replication
repadmin /syncall /AdeP

# Step 8 — Verify clean
repadmin /replsummary            # Should show 0 failures
```

---

## 📋 P1 Examples from This Lab

| Ticket | Issue | Resolution Time |
|--------|-------|----------------|
| `#2026041401` | AD Replication Failure — Error 8524 | 1h 45min ✅ within 4hr SLA |

---

<div align="center">
<sub>🔴 P1 Incident Response Workflow | InfoTech IT Support | itsm-helpdesk-lab</sub>
</div>