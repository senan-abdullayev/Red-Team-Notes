
Once we gain a foothold in the domain, our goal shifts to advancing our position further by moving laterally or vertically to obtain access to other hosts, and eventually achieve domain compromise or some other goal, depending on the aim of the assessment. To achieve this, there are several ways we can move laterally.


There are several other ways we can move around a Windows domain:

- `Remote Desktop Protocol` (`RDP`) - is a remote access/management protocol that gives us GUI access to a target host
- [PowerShell Remoting](https://docs.microsoft.com/en-us/powershell/scripting/learn/ps101/08-powershell-remoting?view=powershell-7.2) - also referred to as PSRemoting or Windows Remote Management (WinRM) access, is a remote access protocol that allows us to run commands or enter an interactive command-line session on a remote host using PowerShell
- `MSSQL Server` - an account with sysadmin privileges on an SQL Server instance can log into the instance remotely and execute queries against the database. This access can be used to run operating system commands in the context of the SQL Server service account through various methods


## Remote Desktop

#### Enumerating the Remote Desktop Users Group

```ruby
Get-NetLocalGroupMember -ComputerName ACADEMY-EA-MS01 -GroupName "Remote Desktop Users"
```

From the information above, we can see that all Domain Users (meaning `all` users in the domain) can RDP to this host.

## Establishing WinRM Session from Windows

```ruby
$password = ConvertTo-SecureString "Klmcargo2" -AsPlainText -Force
```

```ruby
$cred = new-object System.Management.Automation.PSCredential ("INLANEFREIGHT\forend", $password)
```


## Enumerating MSSQL Instances with PowerUpSQL


```ruby
Import-Module .\PowerUpSQL.ps1
```

```ruby
Get-SQLInstanceDomain
```

```ruby
Get-SQLQuery -Verbose -Instance "172.16.5.150,1433" -username "inlanefreight\damundsen" -password "SQL1234!" -query 'Select @@version'
```


