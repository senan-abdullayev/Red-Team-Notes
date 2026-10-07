
```
wmic product get name
```

The output looks mostly standard for a Windows 10 workstation. However, the `Druva inSync` application stands out. A quick Google search shows that version `6.6.3` is vulnerable to a command injection attack via an exposed RPC service. We may be able to use [this](https://www.exploit-db.com/exploits/49211) exploit PoC to escalate our privileges.

### Enumerating Local Ports

Let's do some further enumeration to confirm that the service is running as expected. A quick look with `netstat` shows a service running locally on port `6064`.

```
netstat -ano | findstr 6064
```

### Enumerating Process ID

Next, let's map the process ID (PID) `3324` back to the running process.

```
get-process -Id 3324
```

### Enumerating Running Service

At this point, we have enough information to determine that the Druva inSync application is indeed installed and running, but we can do one last check using the `Get-Service` cmdlet.

```
get-service | ? {$_.DisplayName -like 'Druva*'}
```

