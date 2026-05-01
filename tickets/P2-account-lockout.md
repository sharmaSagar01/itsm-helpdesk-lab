# 🟠 Ticket — P2 — Administrator Account Locked Out

| Field | Details |
|-------|---------|
| **Ticket ID** | `#2026041402` |
| **Priority** | P2 — High |
| **SLA** | SEV-2 (4 hours grace / 24x7) |
| **Help Topic** | Account Locked Out |
| **Department** | IT Support |
| **Assigned To** | Paula Doe |
| **Opened By** | itadmin |
| **Date Opened** | 2026-04-14 11:00 |
| **Date Resolved** | 2026-04-14 12:30 |
| **Status** | Resolved |
| **Source Lab** | Wazuh SIEM Lab — Phase 5 Testing |

---

## 📋 Issue Description

The built-in Administrator account is locked out on **both** Domain Controllers
simultaneously. Neither server can be logged into using domain or local
Administrator credentials.

**Impact:** Full administrative access to the domain is blocked.
No AD management, GPO changes, or server administration can be performed
until access is restored.

**Cause:** During Wazuh brute force testing (generating Event ID `4625`),
the Administrator account was used as the test account instead of a
dedicated throwaway account — triggering the 5-attempt lockout policy.

---

## 🔍 Diagnosis

```powershell
# Attempted on both servers — all failed
# Domain credentials
INFOTECH\Administrator → "Account is currently locked out"

# Local credentials (unknown password)
.\Administrator → Password unknown — account previously unlocked
                  via domain, local password never set
```

**Result:** Both AD domain and local Administrator access unavailable.

---

## 🔧 Resolution Steps

**Recovery method used: Utilman.exe replacement via Windows Recovery Mode**

This technique uses the Windows Recovery environment to replace the
Accessibility button on the login screen with CMD — giving SYSTEM-level
access without credentials.

```
Step 1 — Mount Windows Server 2025 ISO in VMware for VM-WINSERV-01
Step 2 — Boot from ISO → Repair your computer → Troubleshoot → Command Prompt

Step 3 — Replace Utilman.exe with CMD
move C:\Windows\System32\Utilman.exe C:\Windows\System32\Utilman.exe.bak
copy C:\Windows\System32\cmd.exe C:\Windows\System32\Utilman.exe

Step 4 — Reboot normally (boot from hard disk)

Step 5 — At login screen click the Accessibility icon (bottom right)
         → CMD opens as SYSTEM

Step 6 — Reset local Administrator password
net user Administrator Admin@12345!
net user Administrator /active:yes

Step 7 — Log in with new password
```

```powershell
# Step 8 — Restore Utilman.exe immediately after login
move C:\Windows\System32\Utilman.exe.bak C:\Windows\System32\Utilman.exe

# Step 9 — Unlock domain Administrator on both DCs via ADUC
Unlock-ADAccount -Identity "Administrator"

# Step 10 — Reset domain Administrator password
Set-ADAccountPassword -Identity "Administrator" `
    -NewPassword (ConvertTo-SecureString "Admin@12345!" -AsPlainText -Force) -Reset

# Step 11 — Verify
Get-ADUser -Identity "Administrator" -Properties LockedOut |
    Select Name, LockedOut
```

Repeated the same Utilman process on **VM-WINSERV-02** to restore local access.

---

## ✅ Resolution

| Check | Result |
|-------|--------|
| Local Administrator access — Server 01 | ✅ Restored |
| Local Administrator access — Server 02 | ✅ Restored |
| Domain Administrator account unlocked | ✅ Confirmed |
| Utilman.exe restored on both servers | ✅ Confirmed |
| Domain login working | ✅ Confirmed |

**Resolution time:** 1 hour 30 minutes (within SEV-2 8-hour resolution SLA)

---

## 📝 Ticket Thread

```
[11:00] Ticket opened — Administrator locked out on both DCs
[11:10] Paula Doe assigned — began recovery planning
[11:20] Utilman technique selected — ISO mounted on VM-WINSERV-01
[11:35] VM-WINSERV-01 local access restored — Utilman.exe recovered
[11:50] Domain Administrator unlocked — VM-WINSERV-02 ISO mounted
[12:10] VM-WINSERV-02 local access restored — Utilman.exe recovered
[12:30] All access confirmed — ticket resolved and closed
```

---

## 🔒 Prevention

```powershell
# Create a dedicated test account for lockout testing — NEVER use Administrator
New-ADUser -Name "Wazuh Test" -SamAccountName "wazuhtest" `
    -AccountPassword (ConvertTo-SecureString "Test@12345!" -AsPlainText -Force) `
    -Enabled $true -ChangePasswordAtLogon $false

# Exclude built-in Administrator from lockout policy
# Via Default Domain Policy — set lockout threshold to 0 for Admin
# Or use Fine-Grained Password Policy scoped to Administrator
```

---

<div align="center">
<sub>📋 Ticket P2-2026041402 | InfoTech IT Support | itsm-helpdesk-lab</sub>
</div>