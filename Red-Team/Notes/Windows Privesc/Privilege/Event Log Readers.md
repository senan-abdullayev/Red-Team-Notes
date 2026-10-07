### Confirming Group Membership

```
net localgroup "Event Log Readers"
```

### Searching Security Logs Using wevtutil

```
wevtutil qe Security /rd:true /f:text | Select-String "/user"
```

### Searching Security Logs Using Get-WinEvent

```
Get-WinEvent -LogName security | where { $_.ID -eq 4688 -and $_.Properties[8].Value -like '*/user*'} | Select-Object @{name='CommandLine';expression={ $_.Properties[8].Value }}
```

