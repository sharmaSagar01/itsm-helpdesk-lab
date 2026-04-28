# 📋 SLA Policy — InfoTech IT Support

> Service Level Agreement definitions for the `itsm-helpdesk-lab` project.
> All SLA plans are configured in osTicket under **Admin Panel → Manage → SLA Plans**.

---

## SLA Plans

| SLA Plan | Grace Period | Schedule | Maps To |
|----------|-------------|---------|---------|
| `SEV-1` | 1 hour | 24/7 | P1 — Critical |
| `SEV-2` | 4 hours | 24/7 | P2 — High |
| `SEV-3` | 8 hours | Business Hours | P3 — Medium |
| `SEV-4` | 72 hours | Business Hours | P4 — Low |

---

## Priority Definitions

### 🔴 SEV-1 — Critical (P1)

| Field | Details |
|-------|---------|
| **Grace Period** | 1 hour |
| **Schedule** | 24/7 — including weekends and holidays |
| **First Response** | Within 15 minutes |
| **Resolution** | Within 4 hours |
| **Definition** | Complete service outage or critical security incident — all users affected |
| **Examples** | AD replication failure, Domain Controller down, active brute force attack, ransomware detection |
| **Escalation** | Immediately to Level II — no Level I handling |

---

### 🟠 SEV-2 — High (P2)

| Field | Details |
|-------|---------|
| **Grace Period** | 4 hours |
| **Schedule** | 24/7 — including weekends and holidays |
| **First Response** | Within 1 hour |
| **Resolution** | Within 8 hours |
| **Definition** | Service degraded or significant security event — multiple users or sensitive accounts affected |
| **Examples** | Account lockout, failed login alerts from Wazuh, GPO not applying, shared folder access denied for a team |
| **Escalation** | Level I first response — escalate to Level II if not resolved in 2 hours |

---

### 🟡 SEV-3 — Medium (P3)

| Field | Details |
|-------|---------|
| **Grace Period** | 8 hours |
| **Schedule** | Business hours — Monday to Friday 8am–5pm |
| **First Response** | Within 4 hours |
| **Resolution** | Within 24 hours |
| **Definition** | Single user affected — workaround available |
| **Examples** | New user onboarding, password reset, mapped drive not connecting, printer issue |
| **Escalation** | Level I handles — escalate to Level II only if specialist knowledge required |

---

### 🟢 SEV-4 — Low (P4)

| Field | Details |
|-------|---------|
| **Grace Period** | 72 hours |
| **Schedule** | Business hours — Monday to Friday 8am–5pm |
| **First Response** | Within 8 hours |
| **Resolution** | Within 72 hours |
| **Definition** | General requests with no immediate impact |
| **Examples** | Software installation request, IT equipment request, access permission review |
| **Escalation** | Level I handles — no escalation unless request changes in scope |

---

## Help Topic → SLA Mapping

| Help Topic | SLA Plan | Priority | Default Department |
|------------|---------|---------|-------------------|
| `Security Alert` | SEV-1 | Emergency | Security |
| `Server / AD Issue` | SEV-1 | High | Infrastructure |
| `Account Locked Out` | SEV-2 | High | IT Support |
| `New User Onboarding` | SEV-3 | Normal | IT Support |
| `Password Reset` | SEV-3 | Normal | IT Support |
| `General IT Request` | SEV-4 | Low | IT Support |

---

## SLA Breach Procedure

If a ticket breaches its SLA grace period without resolution:

```
1. osTicket automatically flags the ticket as overdue (red indicator)
2. Agent receives an email notification of the SLA breach
3. Department manager is notified
4. Ticket is escalated one priority level
5. Breach is documented in the ticket thread with reason
```

---

<div align="center">
<sub>📋 InfoTech IT Support — SLA Policy | itsm-helpdesk-lab</sub>
</div>