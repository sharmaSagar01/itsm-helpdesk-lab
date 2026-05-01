# 🟡 Ticket — P3 — New User Onboarding

| Field             | Details                                                |
| ----------------- | ------------------------------------------------------ |
| **Ticket ID**     | `#2026041404`                                          |
| **Priority**      | P3 — Normal                                            |
| **SLA**           | SEV-3 (8 hours grace / Business hours)                 |
| **Help Topic**    | New User Onboarding                                    |
| **Department**    | IT Support                                             |
| **Assigned To**   | Dave Doe                                               |
| **Opened By**     | Ram Doe (HR)                                           |
| **Date Opened**   | 2026-04-14 08:30                                       |
| **Date Resolved** | 2026-04-14 11:00                                       |
| **Status**        | Resolved                                               |
| **Source Lab**    | AD Automation Toolkit — Script 1 (New-UserOnboard.ps1) |

---

## 📋 Issue Description

New hire starting on Monday — AD account and access setup required.

```
New Hire Details:
  Name:         Jane Smith
  Department:   IT
  Job Title:    Support Analyst
  Manager:      Paula Doe (paula.doe)
  Start Date:   2026-04-14 (Monday)

Requirements:
  ✅ Create AD account with correct OU and security group assignments
  ✅ Set temporary password — force change at first login
  ✅ Map network drives (IT_Docs shared folder, Personal drive)
  ✅ Confirm access to IT_Staff shared resources
  ✅ Send welcome message with login instructions
```

---

## 🔧 Resolution Steps

**Step 1 — Create AD account using New-UserOnboard.ps1**

```powershell
# Run on VM-DEV-WINSERV-01 as Domain Admin
.\scripts\New-UserOnboard.ps1 `
    -FirstName "Jane" `
    -LastName "Smith" `
    -Department "IT" `
    -JobTitle "Support Analyst" `
    -Manager "paula.doe"
```

Script output confirmed:

```
[SUCCESS] User created in OU: OU=All_Staff,DC=InfoTech,DC=com
[OK]   Added to group: IT_Support
[OK]   Added to group: All_Staff
[OK]   Added to group: Personal

 Name       : Jane Smith
 Username   : jsmith
 UPN        : jsmith@InfoTech.com
 Temp Pass  : apple@123 (must change at first login)
```

**Step 2 — Verify account in AD**

```powershell
Get-ADUser -Identity "jsmith" -Properties Department, Title, MemberOf |
    Select DisplayName, SamAccountName, Department, Title
```

**Step 3 — Verify shared folder access**

Logged into domain-joined client as `jsmith` — confirmed:

- `\\VM-DEV-WINSERV-01\IT_Docs` → ✅ Accessible (IT_Support group)
- Personal drive mapped → ✅ `\\VM-DEV-WINSERV-01\Personal\jsmith`

**Step 4 — Confirm GPO applied**

```powershell
gpresult /r
# Confirmed: Disable_Control_Panel GPO applied via All_Staff OU
# Confirmed: Folder_Redirection_Policy applied
```

**Step 5 — Send welcome instructions to user**

```
Welcome to InfoTech, Jane!

Your IT account is ready:
  Username: jsmith@InfoTech.com
  Temp password: apple@123 (you will be prompted to change this on first login)

Your network drives are mapped automatically when you log in.
For IT support, submit a ticket at: http://192.168.1.xx/osticket

— IT Support Team
```

---

## ✅ Resolution

| Requirement                                   | Result |
| --------------------------------------------- | ------ |
| AD account created (`jsmith`)                 | ✅     |
| Placed in `All_Staff` OU                      | ✅     |
| Added to `IT_Support` security group          | ✅     |
| Added to `All_Staff` distribution group       | ✅     |
| Temporary password set — reset on login       | ✅     |
| IT_Docs shared folder access confirmed        | ✅     |
| Personal network drive mapped                 | ✅     |
| GPOs applied (Control Panel, Folder Redirect) | ✅     |
| Welcome message sent                          | ✅     |

**Resolution time:** 2 hours 30 minutes (within SEV-3 24-hour SLA)

---

## 📝 Ticket Thread

```
[08:30] Ram Doe (HR) opened ticket — new hire starting Monday
[09:00] Dave Doe assigned — began onboarding
[09:15] New-UserOnboard.ps1 executed — account created successfully
[09:30] Group memberships and drive access verified on client machine
[10:30] GPO application confirmed — all policies applying correctly
[11:00] Welcome email sent to Jane Smith — ticket resolved and closed
```

---

## 🔒 Notes

This ticket demonstrates the value of the `New-UserOnboard.ps1` script
from the AD Automation Toolkit — the entire account setup was completed
with a single command rather than multiple manual steps in ADUC.

Estimated time saving vs manual process: ~25 minutes per new hire.

---

<div align="center">
<sub>📋 Ticket P3-2026041404 | InfoTech IT Support | itsm-helpdesk-lab</sub>
</div>
