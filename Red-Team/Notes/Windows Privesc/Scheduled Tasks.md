#### Enumerating Scheduled Tasks

We can use the [schtasks](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/schtasks) command to enumerate scheduled tasks on the system.

```
schtasks /query /fo LIST /v
```

#### Enumerating Scheduled Tasks with PowerShell

We can also enumerate scheduled tasks using the [Get-ScheduledTask](https://docs.microsoft.com/en-us/powershell/module/scheduledtasks/get-scheduledtask?view=windowsserver2019-ps) PowerShell cmdlet.

```
Get-ScheduledTask | select TaskName,State
```

#### Checking Permissions on C:\Scripts Directory

```
.\accesschk64.exe /accepteula -s -d C:\Scripts\
```



___


## User/Computer Description Field

Though more common in Active Directory, it is possible for a sysadmin to store account details (such as a password) in a computer or user's account description field. We can enumerate this quickly for local users using the [Get-LocalUser](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.localaccounts/get-localuser?view=powershell-5.1) cmdlet.

```
Get-LocalUser
```

#### Enumerating Computer Description Field with Get-WmiObject Cmdlet

We can also enumerate the computer description field via PowerShell using the [Get-WmiObject](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/get-wmiobject?view=powershell-5.1) cmdlet with the [Win32_OperatingSystem](https://docs.microsoft.com/en-us/windows/win32/cimwin32prov/win32-operatingsystem) class.

```
Get-WmiObject -Class Win32_OperatingSystem | select Description
```

