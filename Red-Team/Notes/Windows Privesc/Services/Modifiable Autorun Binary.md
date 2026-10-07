
Not a service, but the same logic applied to startup programs — if you can write to a binary (or its registry Run key) that launches when another user logs in, you get code execution in _their_ context next login.

**Enumerate autoruns**

```
Get-CimInstance Win32_StartupCommand | select Name, command, Location, User |fl
```

Check the resulting binary paths with `icacls` and the registry `Run` keys with `accesschk`, same as above. If `mrb3n`'s OneDrive.exe path or the registry value pointing to it is writable by you, swap it for a payload and wait for them to log in.


