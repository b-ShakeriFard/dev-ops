# LDAP

Lightweight LDAP setup and administration using OpenLDAP on Debian Linux.

## Topics

- OpenLDAP installation
- LDAP directory structure
- Users and groups
- Basic LDAP queries
- LDAP administration commands

## Lab Environment

- Debian 12 Bookworm
- 32-bit x86 (`i686`)
- OpenLDAP (`slapd`)
- `ldap-utils`

## Directory Structure

```text
dc=debian-system,dc=local
├── ou=people
│   └── uid=users
└── ou=groups
    └── cn=groups