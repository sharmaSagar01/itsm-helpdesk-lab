# 🔗 Authentication — LDAP Attempt & Local Auth Decision

> Documents the LDAP integration that was attempted in Phase 4,
> the technical issue encountered, and the decision to use local authentication.

---

## 🎯 What Was Attempted

LDAP authentication was attempted to allow osTicket agents to log in
using their existing `InfoTech.com` Active Directory credentials —
eliminating the need for a separate osTicket password.

---

## 🔧 Steps Completed

**Step 1 — PHP LDAP Extension**
```bash
sudo apt install php-ldap -y
sudo systemctl restart apache2
php -m | grep ldap   # confirmed: ldap loaded
```

**Step 2 — Build auth-ldap Plugin from Source**

The default plugin from master branch was incompatible with osTicket 1.18.x
(caused `LDAPAuthentication contains 5 abstract methods` fatal error).
Built the plugin as a `.phar` instead:

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
```

Plugin appeared in Admin Panel → Manage → Plugins as Active ✅

**Step 4 — Install Net_LDAP2 Dependency**
```bash
sudo apt install php-pear -y
sudo pear install Net_LDAP2
# Confirmed installed: LDAP2, LDAP2.php in /usr/share/php/Net/
```

**Step 5 — Configure Plugin**

Configured with:
- Default Domain: `InfoTech.com`
- DNS Server: `192.168.1.10`
- Search User: `CN=Administrator,CN=Users,DC=InfoTech,DC=com`
- Search Base: `DC=InfoTech,DC=com`
- LDAP Schema: Auto-detect

---

## ❌ Issue Encountered

After saving the LDAP instance configuration, osTicket returned:

```
Failed opening required 'include/Net/LDAP2.php'
(include_path='.:/var/www/html/osticket/include/:
/var/www/html/osticket/include/pear/:
phar:///var/www/html/osticket/include/plugins/auth-ldap.phar/include')
```

### Root Cause

`Net_LDAP2` was installed by PEAR to `/usr/share/php/Net/` but the
`auth-ldap.phar` plugin's internal include path does not reach that location.
It only searches within its own phar archive and osTicket's include directory.

### Resolution Attempts

| Attempt | Command | Result |
|---------|---------|--------|
| Add PEAR to php.ini include_path | `include_path = ".:/usr/share/php"` | ❌ Failed |
| Symlink Net into osTicket include | `ln -s /usr/share/php/Net /var/www/html/osticket/include/Net` | ❌ Failed |
| Copy Net directly into osTicket | `sudo cp -r /usr/share/php/Net /var/www/html/osticket/include/` | ❌ Failed |

### Conclusion

Known version incompatibility between:
- **osTicket 1.18.1**
- **auth-ldap plugin** (built from latest source)
- **Net_LDAP2 2.2.0** path resolution on **Ubuntu 26**

The plugin's internal phar include path does not support external PEAR
library resolution on newer Ubuntu releases. This is a documented community
issue without an official fix for osTicket 1.18.x on Ubuntu 26.

---

## ✅ Decision — Local Authentication

osTicket's built-in local authentication was implemented instead.

**Why this is acceptable:**
- Local auth is standard in the majority of real production osTicket deployments
- All ITSM functionality — tickets, SLAs, escalations, workflows — is unaffected
- Agent usernames mirror AD identities for portfolio consistency
- The LDAP troubleshooting process itself demonstrates real diagnostic skills

**Agent accounts configured:**

| Agent | Username | Mirrors AD Identity |
|-------|---------|-------------------|
| Admin User | `itadmin` | `Administrator` |
| Paula Doe | `paula.doe` | `paula.doe@InfoTech.com` |
| Dave Doe | `dave.doe` | `dave.doe@InfoTech.com` |
| Sue | `sue` | `sue@InfoTech.com` |

---

## 📝 Lessons Learned

1. Always verify plugin compatibility against the **exact osTicket version** before installing
2. PEAR-based PHP libraries installed system-wide are not accessible inside `.phar` archives without explicit path configuration
3. The correct approach for future resolution would be to bundle `Net_LDAP2` inside the plugin's phar during the build process using composer

---

<div align="center">
<sub>🔗 Authentication Reference | InfoTech.com | itsm-helpdesk-lab</sub>
</div>