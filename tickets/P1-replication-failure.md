# 🔴 Ticket — P1 — AD Replication Failure

| Field | Details |
|-------|---------|
| **Ticket ID** | `#2026041401` |
| **Priority** | P1 — Critical |
| **SLA** | SEV-1 (1 hour grace / 24x7) |
| **Help Topic** | Server / AD Issue |
| **Department** | Infrastructure |
| **Assigned To** | Paula Doe |
| **Opened By** | itadmin |
| **Date Opened** | 2026-04-14 09:00 |
| **Date Resolved** | 2026-04-14 10:45 |
| **Status** | Resolved |
| **Source Lab** | AD & Windows Server Labs — Lab 8 |

---

## 📋 Issue Description

AD replication is failing between `VM-DEV-WINSERV-01` and `VM-DEV-WINSERV-02`.

```
Error: 8524 (0x214c)
The DSA operation is unable to proceed because of a DNS lookup failure.

Consecutive failures: 10
Failing since: 2026-04-06 09:08:50
Affected partitions:
  - DC=InfoTech,DC=com
  - CN=Configuration,DC=InfoTech,DC=com
  - CN=Schema,CN=Configuration,DC=InfoTech,DC=com
  - DC=ForestDnsZones,DC=InfoTech,DC=com
Successful partition:
  - DC=DomainDnsZones,DC=InfoTech,DC=com
```

**Impact:** Changes on the primary DC are not replicating to the secondary.
The domain is effectively running on a single DC — fault tolerance is lost.

---

## 🔍 Diagnosis

```powershell
# Step 1 — Confirm replication failure
repadmin /showrepl
# Result: 10 consecutive failures — error 8524 on all partitions

# Step 2 — Test basic connectivity — network is fine
ping 192.168.1.12
# Result: Replies received — network not the issue

# Step 3 — DNS lookup test
nslookup 4b7b0d52-5df9-4f0c-a52b-8f001d6e2feb._msdcs.InfoTech.com
# Result: Request timed out (Server: Unknown, Address: ::1)
# IPv6 DNS loopback timing out before IPv4 fallback
```

**Root cause identified:**
1. VM-DEV-WINSERV-02 DNS pointing to itself as preferred — cannot resolve
   `_msdcs` CNAME records before primary DC has replicated them
2. IPv6 DNS (`::1`) timing out and causing lookup failure before
   IPv4 fallback kicks in

---

## 🔧 Resolution Steps

```powershell
# Fix 1 — Update DNS on VM-DEV-WINSERV-02
# Changed preferred DNS from 192.168.1.12 (itself) → 192.168.1.10 (Server 1)
# Changed alternate DNS from 192.168.1.10 → 192.168.1.12 (itself)

# Fix 2 — Re-register DNS on both servers
ipconfig /registerdns
nltest /dsregdns
ipconfig /flushdns
Restart-Service -Name Dnscache

# Fix 3 — Disable IPv6 on both servers
Disable-NetAdapterBinding -Name "Ethernet0" -ComponentID ms_tcpip6

# Fix 4 — Force replication
repadmin /syncall /AdeP

# Fix 5 — Verify resolution
repadmin /replsummary
# Result: 0 failures on both DCs
```

---

## ✅ Resolution

| Check | Result |
|-------|--------|
| `DC=InfoTech,DC=com` replication | ✅ Successful |
| `CN=Configuration` replication | ✅ Successful |
| `CN=Schema` replication | ✅ Successful |
| `DC=DomainDnsZones` replication | ✅ Successful |
| `DC=ForestDnsZones` replication | ✅ Successful |
| `repadmin /replsummary` failures | ✅ 0 / 5 |

**Resolution time:** 1 hour 45 minutes (within SEV-1 4-hour resolution SLA)

---

## 📝 Ticket Thread

```
[09:00] itadmin opened the ticket — replication failures detected
[09:15] Paula Doe assigned — began investigation
[09:30] Root cause identified — DNS misconfiguration on VM-DEV-WINSERV-02
[09:45] DNS settings corrected on both servers — repadmin /syncall running
[10:15] Replication confirmed clean on 4 of 5 partitions
[10:45] All 5 partitions confirmed — ticket resolved and closed
```

---

## 🔒 Prevention

- Secondary DC should always point to the primary DC as preferred DNS
- IPv6 should be disabled on all DCs unless explicitly required
- Run `repadmin /replsummary` as part of the weekly AD health check
- Use `Get-ADHealthCheck.ps1` from the AD Automation Toolkit for automated monitoring

---

<div align="center">
<sub>📋 Ticket P1-2026041401 | InfoTech IT Support | itsm-helpdesk-lab</sub>
</div>