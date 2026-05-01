# 📋 SLA Policy — InfoTech IT Support

> Service Level Agreement definitions for the `itsm-helpdesk-lab` project.
> Configured in osTicket under **Admin Panel → Manage → SLA Plans**.

---

## SLA Plans Summary

| Plan | Grace Period | Schedule | Priority |
|------|-------------|---------|---------|
| `SEV-1` | 1 hour | 24/7 | P1 — Critical |
| `SEV-2` | 4 hours | 24/7 | P2 — High |
| `SEV-3` | 8 hours | Business hours (M–F 8am–5pm) | P3 — Medium |
| `SEV-4` | 72 hours | Business hours (M–F 8am–5pm) | P4 — Low |

---

## SEV-1 — Critical (P1)

| Field | Details |
|-------|---------|
| **Grace Period** | 1 hour |
| **Schedule** | 24/7 including weekends and holidays |
| **First Response** | Within 15 minutes |
| **Resolution Target** | Within 4 hours |
| **Definition** | Complete service outage or critical security incident — all users affected or core infrastructure down |
| **Examples** | AD replication failure, Domain Controller offline, ransomware detection, domain-wide authentication failure |
| **Assigned To** | Level II immediately — no Level I handling |
| **Escalation** | itadmin notified at T+0 |

---

## SEV-2 — High (P2)

| Field | Details |
|-------|---------|
| **Grace Period** | 4 hours |
| **Schedule** | 24/7 including weekends and holidays |
| **First Response** | Within 1 hour |
| **Resolution Target** | Within 8 hours |
| **Definition** | Significant security event or service degradation — specific users or accounts affected |
| **Examples** | Account lockout, Wazuh security alert, GPO not applying, shared folder inaccessible for a team |
| **Assigned To** | Level I first — escalate to Level II if not resolved in 2 hours |

---

## SEV-3 — Medium (P3)

| Field | Details |
|-------|---------|
| **Grace Period** | 8 hours |
| **Schedule** | Business hours — Monday to Friday 8am–5pm |
| **First Response** | Within 4 hours |
| **Resolution Target** | Within 24 hours |
| **Definition** | Single user affected — workaround available or no immediate business impact |
| **Examples** | New user onboarding, password reset, mapped drive not connecting, software access request |
| **Assigned To** | Level I handles — escalate to Level II only if specialist knowledge required |

---

## SEV-4 — Low (P4)

| Field | Details |
|-------|---------|
| **Grace Period** | 72 hours |
| **Schedule** | Business hours — Monday to Friday 8am–5pm |
| **First Response** | Within 8 hours |
| **Resolution Target** | Within 72 hours |
| **Definition** | General IT requests with no immediate operational impact |
| **Examples** | Software installation request, IT equipment request, access permission review, general enquiry |
| **Assigned To** | Level I handles — no escalation unless scope changes |

---

## Help Topic → SLA Mapping

| Help Topic | SLA | Priority | Default Department |
|------------|-----|---------|-------------------|
| `Security Alert` | SEV-1 | Emergency | Security |
| `Server / AD Issue` | SEV-1 | High | Infrastructure |
| `Account Locked Out` | SEV-2 | High | IT Support |
| `New User Onboarding` | SEV-3 | Normal | IT Support |
| `Password Reset` | SEV-3 | Normal | IT Support |
| `General IT Request` | SEV-4 | Low | IT Support |

---

## SLA Breach Procedure

```
1. osTicket automatically flags the ticket as overdue (red indicator in queue)
2. Assigned agent receives a breach notification
3. Department manager is notified
4. Ticket is reviewed for escalation — priority raised if appropriate
5. Breach reason is documented in the ticket thread
6. Reported in the weekly SLA compliance review
```

---

<div align="center">
<sub>📋 SLA Policy | InfoTech IT Support | itsm-helpdesk-lab</sub>
</div>