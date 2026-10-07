

### Discover the Base DN First

```
ldapsearch -H ldap://htb.local -x -s base namingcontexts
```


### Full Anonymous Enumeration

```
ldapsearch -H ldap://htb.local -x -b "DC=htb,DC=local"
```


### Enumerate all users:

```
ldapsearch -H ldap://htb.local -x -b "DC=htb,DC=local" "(objectClass=user)" sAMAccountName cn description
```


### Via Credentials

```
ldapsearch -H ldap://administrator.htb -x \
  -D "olivia@administrator.htb" \
  -w 'ichliebedich' \
  -b "DC=administrator,DC=htb" \
  "(objectClass=user)" sAMAccountName cn description
```

