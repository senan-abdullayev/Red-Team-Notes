
#### Tasklist

```
tasklist /svc
```


The environment variables explain a lot about the host configuration. To get a printout of them, Windows provides the `set` command. One of the most overlooked variables is `PATH`. In the output below, nothing is out of the ordinary. However, it is not uncommon to find administrators (or applications) modify the `PATH`. One common example is to place Python or Java in the path, which would allow the execution of Python or . JAR files. If the folder placed in the PATH is writable by your user, it may be possible to perform DLL Injections against other applications. Remember, when running a program, Windows looks for that program in the CWD (Current Working Directory) first, then from the PATH going left to right. This means if the custom path is placed on the left (before C:\Windows\System32), it is much more dangerous than on the right.

```
set
```


#### View Detailed Configuration Information

The `systeminfo` command will show if the box has been patched recently and if it is a VM. If the box has not been patched recently, getting administrator-level access may be as simple as running a known exploit. Google the KBs installed under [HotFixes](https://www.catalog.update.microsoft.com/Search.aspx?q=hotfix) to get an idea of when the box has been patched. This information isn't always present, as it is possible to hide hotfixes software from non-administrators. The `System Boot Time` and `OS Version` can also be checked to get an idea of the patch level. If the box has not been restarted in over six months, chances are it is also not being patched.

```
systeminfo
```


#### Patches and Updates

If `systeminfo` doesn't display hotfixes, they may be queriable with [WMI](https://docs.microsoft.com/en-us/windows/win32/wmisdk/wmi-start-page) using the WMI-Command binary with [QFE (Quick Fix Engineering)](https://docs.microsoft.com/en-us/windows/win32/cimwin32prov/win32-quickfixengineering) to display patches.

```
wmic qfe
```

We can do this with PowerShell as well using the [Get-Hotfix](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/get-hotfix?view=powershell-7.1) cmdlet.

```
Get-HotFix | ft -AutoSize
```

#### Installed Programs

WMI can also be used to display installed software. This information can often guide us towards hard-to-find exploits. Is `FileZilla`/`Putty`/etc installed? Run `LaZagne` to check if stored credentials for those applications are installed. Also, some programs may be installed and running as a service that is vulnerable.

```
wmic product get name
```


We can, of course, do this with PowerShell as well using the [Get-WmiObject](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/get-wmiobject?view=powershell-5.1) cmdlet.

```
Get-WmiObject -Class Win32_Product |  select Name, Version
```


#### Display Running Processes

```
netstat -ano
```


#### Logged-In Users

It is always important to determine what users are logged into a system. Are they idle or active? Can we determine what they are working on? While more challenging to pull off, we can sometimes attack users directly to escalate privileges or gain further access. During an evasive engagement, we would need to tread lightly on a host with other user(s) actively working on it to avoid detection.

```
query user
```


#### Current User

When we gain access to a host, we should always check what user context our account is running under first. Sometimes, we are already SYSTEM or equivalent! Suppose we gain access as a service account. In that case, we may have privileges such as `SeImpersonatePrivilege`, which can often be easily abused to escalate privileges using a tool such as [Juicy Potato](https://github.com/ohpe/juicy-potato).

```
echo %USERNAME%
```



#### Current User Privileges

```
whoami /priv
```


#### Group Information

```
whoami /groups
```


#### Getting All Users and All Groups

```
net user
```

```
net localgroup
```


#### Getting information about other user / group

```
net user john
```

```
net localgroup administrators
```
