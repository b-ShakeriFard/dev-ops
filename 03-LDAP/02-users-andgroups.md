2. Verify the Service
sudo systemctl status slapd

Test LDAP locally:

```bash
ldapsearch -x \
  -H ldap://localhost \
  -b "dc=debian-system,dc=local"
```

A successful query should return:

```bash
result: 0 Success
```
<hr>

3. Create Organizational Units

Create:

```bash
nano base-structure.ldif
```

Add
```bash
dn: ou=people,dc=debian-system,dc=local
objectClass: organizationalUnit
ou: people

dn: ou=groups,dc=debian-system,dc=local
objectClass: organizationalUnit
ou: groups
```
Import structure:

```bash
ldapadd -x \
  -H ldap://localhost \
  -D "cn=admin,dc=debian-system,dc=local" \
  -W \
  -f base-structure.ldif
```

