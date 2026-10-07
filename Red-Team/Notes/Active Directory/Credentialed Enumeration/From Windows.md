
By default, PowerShell doesn't know how to talk to Active Directory. You have to load the specific Active Directory toolkit first before you can run AD-specific commands.

Run `Get-Module` to see which toolkits are currently active in their PowerShell session.

```ruby
Get-Module
```

If ActiveDirectory module is not there we can import it with : 

```ruby
Import-Module ActiveDirectory
```


## Get-ADDomain

This command acts like a blueprint for the network. It gives you the fundamental details of the domain you are currently sitting in.

```ruby
Get-ADDomain
```

## Get-ADUser

- **What it does:** It searches the entire directory specifically for user accounts that have a "Service Principal Name" (SPN) attached to them. These are typically "Service Accounts" used to run background applications (like backups or databases).
    
- **Why it matters:** Service accounts are the exact targets needed for a **Kerberoasting** attack. Finding them is a major step toward extracting and offline-cracking high-privilege passwords.


```ruby
Get-ADUser -Filter {ServicePrincipalName -ne "$null"} -Properties ServicePrincipalName
```


## Get-ADTrust

- **What it does:** It lists out all the "Trust Relationships." This tells you which other domains or forests trust the current domain, and whether that trust goes one way or both ways (BiDirectional).
    
- **Why it matters:** Trusts are bridges. If you take over your current domain, a trust relationship might give you the permission needed to pivot and attack a completely different network (like moving from the `INLANEFREIGHT` domain to the `LOGISTICS` domain).

```ruby
Get-ADTrust -Filter *
```


## Get-ADGroup

Active Directory uses groups to assign permissions.

- **What it does:** It simply lists every single group that exists in the domain (e.g., _Domain Admins_, _Backup Operators_, _Remote Desktop Users_).
    
- **Why it matters:** It shows you the different tiers of access. Instead of guessing how permissions are handed out, you can look for groups that clearly have elevated privileges.

```ruby
Get-ADGroup -Filter * | select name
```


## Get-ADGroupMember


Once you find an interesting group from the previous command, you need to know who is inside it.

- **What it does:** You feed it a specific group name (like `"Backup Operators"`), and it lists out every user account that belongs to that group.
    
- **Why it matters:** High-value groups like _Domain Admins_ are usually heavily monitored and protected. However, groups like _Backup Operators_ still have massive privileges (the ability to read almost any file on the system) but are often less protected. By finding out that an account named `backupagent` is in this group, you now have a specific, potentially weaker target to attack in order to escalate your privileges.

```ruby
Get-ADGroupMember -Identity "Backup Operators"
```


## Powerview

```ruby
. .\Powerview.ps1
```

### Get-DomainUser

Instead of grabbing all users, this command pulls the detailed profile of a single user

```ruby
Get-DomainUser -Identity mmorgan -Domain inlanefreight.local | Select-Object -Property
```

### Get-DomainGroupMember -Recurse

This is one of PowerView's most powerful features. Active Directory allows groups to be placed inside other groups (Nested Groups).

```ruby
Get-DomainGroupMember -Identity "Domain Admins" -Recurse
```

- **What it does:** By adding the `-Recurse` flag to the "Domain Admins" group, PowerView doesn't just list the direct members; it digs into every group inside that group, and every group inside _those_ groups.
    
- **Why it matters:** The output reveals that a user named `Maggie Jablonski` is in the `Secadmins` group. Because `Secadmins` is secretly nested inside `Domain Admins`, Maggie has full control over the entire domain, even though a standard check wouldn't show her as a Domain Admin.

### Get-DomainTrustMapping

This is similar to the native PowerShell `Get-ADTrust` you looked at earlier, but it maps out all known relationships in the environment automatically.

```ruby
Get-DomainTrustMapping
```


### Test-AdminAccess

This is a more active, slightly "louder" command.

**What it does:** It takes your current compromised credentials and reaches out to a specific machine (`ACADEMY-EA-MS01`) to see if you have Local Administrator privileges there.

**Why it matters:** If it returns `True`, you know you can immediately drop a payload, dump memory, or use `psexec.py` on that machine to further your access.

```ruby
Test-AdminAccess -ComputerName ACADEMY-EA-MS01
```


### Get-DomainUser -SPN


This is the fastest way to line up targets for offline password cracking.

- **What it does:** It filters the entire domain for user accounts that have a Service Principal Name (SPN) attached to them, outputting a clean list of the usernames and the services they run.
    
- **Why it matters:** Every single account on this list (like `sqldev`, `sqlprod`, `backupagent`) is a target for **Kerberoasting**. You can request their service tickets, export them to your attacking machine, and run them through Hashcat to crack their passwords.

```ruby
Get-DomainUser -SPN -Properties samaccountname,ServicePrincipalName
```



# Snaffler


**Snaffler**, which is essentially an automated "treasure hunter" for Active Directory networks.

Snaffler hunts for misconfigured **files and data**. It scans every computer in the domain, looks at every file share it has access to, and flags anything that looks like it might contain a password, a secret key, or sensitive corporate data.

```ruby
Snaffler.exe -s -d inlanefreight.local -o snaffler.log -v data
```



## SharpHound.exe

running the SharpHound.exe collector from the MS01 attack host.

```ruby
.\SharpHound.exe -c All --zipfilename ILFREIGHT
```



