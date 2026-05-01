# 🔗 Authentication — LDAP Attempt & Local Auth Decision

> Documents the LDAP integration attempted in Phase 4, the technical
> issue encountered, and the decision to use local authentication instead.

---

## 🎯 What Was Attempted

LDAP authentication was attempted to allow osTicket agents to log in with
their existing `InfoTech.com` Active Directory credentials — eliminating
the need for a separate osTicket password.

**Target configuration:**

| Field | Value |
|-------|-------|
| LDAP Server | `192.168.1.10` (VM-WINSERV-01) |
| Port | `389` |
| Base DN | `DC=InfoTech,DC=com` |
| Bind DN | `CN=Administrator,CN=Users,DC=InfoTech,DC=com` |
| Search Filter | `(sAMAccountName=%s)` |

---

## 🔧 Steps Completed

**Step 1 — PHP LDAP Extension**
```bash
sudo apt install php-ldap -y
sudo systemctl restart apache2
php -m | grep ldap   # ✅ confirmed: ldap loaded
```

**Step 2 — Build auth-ldap Plugin from Source**

The plugin from the master branch caused a fatal PHP error
(`LDAPAuthentication contains 5 abstract methods`) due to an incompatible
class interface in osTicket 1.18.x. Built the plugin as a `.phar` instead:

```bash
cd /tmp
git clone https://github.com/osTicket/osTicket-plugins.git
cd osTicket-plugins
composer install --no-dev
php -dphar.readonly=0 make.php build auth-ldap
```

**Step 3 — Deploy Plugin**
```bash
sudo cp auth-ldap.phar /var/www/html/osticket/include/plugins/
sudo chown www-data:www-data /var/www/html/osticket/include/plugins/auth-ldap.phar
sudo systemctl restart apache2
# ✅ Plugin appeared in Admin Panel → Manage → Plugins as Active
```

**Step 4 — Install Net_LDAP2**
```bash
sudo apt install php-pear -y
sudo pear install Net_LDAP2
# ✅ Installed: LDAP2, LDAP2.php confirmed in /usr/share/php/Net/
```

**Step 5 — Configure the Plugin**

Configured via Admin Panel → Manage → Plugins → LDAP → Config:
- Default Domain: `InfoTech.com`
- DNS Server: `192.168.1.10`
- Search User: `CN=Administrator,CN=Users,DC=InfoTech,DC=com`
- Search Base: `DC=InfoTech,DC=com`
- LDAP Schema: Automatically Detect

---

## ❌ Error After Saving

```
Failed opening required 'include/Net/LDAP2.php'
(include_path='.:/var/www/html/osticket/include/:
/var/www/html/osticket/include/pear/:
phar:///var/www/html/osticket/include/plugins/auth-ldap.phar/include')
```

---

## 🔍 Root Cause

`Net_LDAP2` was installed by PEAR to `/usr/share/php/Net/` — a system-wide
location. The `auth-ldap.phar` plugin only searches within its own phar
archive and osTicket's `include/` directory. It cannot reach
system-wide PEAR libraries without them being explicitly present in those paths.

---

## 🔧 Resolution Attempts

| # | Attempt | Command | Result |
|---|---------|---------|--------|
| 1 | Add PEAR path to `php.ini` | `include_path = ".:/usr/share/php"` | ❌ Failed |
| 2 | Symlink Net into osTicket | `ln -s /usr/share/php/Net /var/www/html/osticket/include/Net` | ❌ Failed |
| 3 | Copy Net into osTicket | `sudo cp -r /usr/share/php/Net /var/www/html/osticket/include/` | ❌ Failed |

**Conclusion:** Known version incompatibility between osTicket 1.18.1,
the `auth-ldap` plugin, and `Net_LDAP2` path resolution on Ubuntu 26.
The `.phar` archive's internal include path cannot be extended to reach
external system libraries without rebuilding the phar with the dependency bundled inside.

---

## ✅ Decision — Local Authentication

osTicket's built-in local authentication implemented instead.

**Why this is acceptable:**
- Local auth is standard in the majority of real production osTicket deployments
- All ITSM functionality — tickets, SLAs, escalations, workflows — is completely unaffected
- Agent usernames mirror AD identities for portfolio consistency
- The full LDAP troubleshooting process demonstrates real diagnostic skills

**Agents configured:**

| Agent | Username | Mirrors AD Identity |
|-------|---------|-------------------|
| Admin User | `itadmin` | `Administrator` |
| Paula Doe | `paula` | `paula.doe@InfoTech.com` |
| Dave Doe | `dave` | `dave.doe@InfoTech.com` |
| Sue | `sue` | `sue@InfoTech.com` |

---

## 📝 Lessons Learned

1. Always verify plugin compatibility against the **exact osTicket version** before installing
2. PEAR libraries installed system-wide are not accessible inside `.phar` archives without bundling
3. The correct resolution would be to rebuild the phar with `Net_LDAP2` bundled via composer during the build step
4. Document compatibility issues honestly — the troubleshooting process is more valuable portfolio content than a clean result

---

<div align="center">
<sub>🔗 Authentication Reference | InfoTech.com | itsm-helpdesk-lab</sub>
</div>