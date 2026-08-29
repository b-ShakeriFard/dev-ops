
---

### `installation.md`

```markdown
# OpenLDAP Installation

This guide installs and configures OpenLDAP on Debian 12.

## 1. Install Packages

```bash
sudo apt update
sudo apt install slapd ldap-utils
```

Reconfigure the LDAP server:

```bash
sudo dpkg-reconfigure slapd
```

Example configuration:

```bash
DNS domain name: debian-system.local
Organization: behroox-home lab
Database backend: MDB
```

This creates the LDAP base DN:

```bash
dc=debian-system,dc=local
```

The administrator DN becomes:

```bash
cn=admin,dc=debian-system,dc=local
```

2. Verify the Service
sudo systemctl status slapd

Test LDAP locally:

```bash
ldapsearch -x \
  -H ldap://localhost \
  -b "dc=debian-system,dc=local"
```

A successful query should return:

result: 0 Success