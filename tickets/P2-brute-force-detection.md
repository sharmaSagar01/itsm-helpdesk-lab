# 🟠 Ticket — P2 — Brute Force Detection Alert

| Field | Details |
|-------|---------|
| **Ticket ID** | `#2026041403` |
| **Priority** | P2 — High |
| **SLA** | SEV-2 (4 hours grace / 24x7) |
| **Help Topic** | Security Alert |
| **Department** | Security |
| **Assigned To** | Sue |
| **Opened By** | itadmin |
| **Date Opened** | 2026-04-15 06:55 |
| **Date Resolved** | 2026-04-15 08:00 |
| **Status** | Resolved |
| **Source Lab** | Wazuh SIEM Lab — Phase 7 Rule Testing |

---

## 📋 Issue Description

Wazuh SIEM generated a **Level 12 (High)** alert at 06:55 — Rule `100102`.

```
Alert: Brute Force Detected
Rule:  100102 — 5+ failed logins for same user within 2 minutes
Level: 12 (High)
Agent: VM-WINSERV-01 (192.168.1.10)

Affected account: INFOTECH\wazuhtest
Source IP:        192.168.1.105
Workstation:      CLIENT-WIN11
Event IDs:        4625 (x5) → 4740 (lockout)
Time window:      06:50:45 — 06:51:17 (32 seconds)
```

Secondary alert also fired:
```
Rule:  100001 — AD Account Lockout
Level: 10 (High)
Account: wazuhtest locked out on INFOTECH
```

---

## 🔍 Diagnosis

```powershell
# Step 1 — Verify alert in Wazuh dashboard
# Navigated to: Threat Hunting → VM-WINSERV-01
# Searched: rule.id: 100102 OR rule.id: 100001
# Confirmed: 5 x 4625 events in 32 seconds + 1 x 4740

# Step 2 — Identify source IP from Security log
Get-WinEvent -FilterHashtable @{ LogName='Security'; Id=4625 } -MaxEvents 10 |
    Select TimeCreated,
    @{N="User";  E={$_.Properties[5].Value}},
    @{N="Source";E={$_.Properties[19].Value}}

# Result: All 5 failures from 192.168.1.105 (CLIENT-WIN11)

# Step 3 — Determine if source is legitimate
# 192.168.1.105 = domain-joined Windows 11 client — internal machine
# wazuhtest = dedicated test account created for Wazuh rule testing
# Confirmed: intentional test — not a genuine attack
```

---

## 🔧 Resolution Steps

```powershell
# Step 1 — Unlock the wazuhtest account
Unlock-ADAccount -Identity "wazuhtest"

# Verify unlocked
Get-ADUser -Identity "wazuhtest" -Properties LockedOut |
    Select Name, LockedOut
# Result: LockedOut = False

# Step 2 — Confirm rule fired correctly in Wazuh
# Rule 100101 — Level 7 — fired on each individual 4625 event ✅
# Rule 100102 — Level 12 — fired after 5th failure (brute force) ✅
# Rule 100001 — Level 10 — fired on 4740 lockout event ✅

# Step 3 — Document as successful rule validation
# No further action required — source was internal test account
```

---

## ✅ Resolution

| Check | Result |
|-------|--------|
| Source IP identified | ✅ Internal — 192.168.1.105 (CLIENT-WIN11) |
| Confirmed intentional test | ✅ wazuhtest account used for rule validation |
| wazuhtest account unlocked | ✅ |
| Wazuh rules confirmed working | ✅ All three rules fired in correct sequence |
| No external threat present | ✅ |

**Resolution time:** 1 hour 5 minutes (within SEV-2 8-hour SLA)

---

## 📝 Ticket Thread

```
[06:55] Wazuh alert triggered — ticket auto-created from security alert
[07:00] Sue assigned — began investigation
[07:10] Source IP confirmed as internal (192.168.1.105)
[07:20] Confirmed as intentional test — wazuhtest account
[07:30] Account unlocked — rules validated as working correctly
[08:00] Ticket closed — no threat, rules functioning as expected
```

---

## 🔒 Wazuh Rules Validated by This Ticket

| Rule | Event ID | Level | Result |
|------|---------|-------|--------|
| `100101` | `4625` | 7 | ✅ Fired on each individual failure |
| `100102` | `4625` x5 | 12 | ✅ Fired after 5 failures in 2 minutes |
| `100001` | `4740` | 10 | ✅ Fired on account lockout |

---

<div align="center">
<sub>📋 Ticket P2-2026041403 | InfoTech IT Support | itsm-helpdesk-lab</sub>
</div>