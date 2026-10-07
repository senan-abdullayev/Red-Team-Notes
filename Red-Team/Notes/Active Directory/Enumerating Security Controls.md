
## Checking the Status of Windows Defender

```ruby
Get-MpComputerStatus
```

```ruby
RealTimeProtectionEnabled       : True
```


## Using Get-AppLockerPolicy cmdlet

```ruby
Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections
```
this command shows the actual, active AppLocker rules currently enforcing what programs can and cannot run on this computer.



## PowerShell Constrained Language Mode

By default, PowerShell runs in **Full Language Mode**, which makes it incredibly powerful. It can interact deeply with the Windows operating system.

```ruby
$ExecutionContext.SessionState.LanguageMode
```

**Constrained Language Mode** is essentially putting PowerShell into a sandbox. When this mode is turned on, PowerShell can still run basic commands (like `cd`, `dir`, or `ping`), but it blocks the advanced



## LAPS (Local Administrator Password Solution)

Historically, IT departments used the same local administrator password for every computer on their network. If an attacker managed to steal that one password from a single compromised laptop, they could immediately use it to log into every other computer and server on the network.

`LAPS` forces every single computer to have a **unique, complex, and constantly changing** local administrator password.


```ruby
Find-LAPSDelegatedGroups
```

it shows the list of users or groups that have been granted permission to read these secret LAPS passwords.


## Hunting the Misconfiguration

```ruby
Find-AdmPwdExtendedRights
```

This command scans every computer object with LAPS enabled to see exactly who has that access.

## Executing the Credential Access

```ruby
Get-LAPSComputers
```

We can use the `Get-LAPSComputers` function to search for computers that have LAPS enabled when passwords expire, and even the randomized passwords in cleartext if our user has access.