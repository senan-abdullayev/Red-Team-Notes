
## PSCredential Object

Create the credential object — store the password securely

```ruby
$SecPassword = ConvertTo-SecureString '!qazXSW@' -AsPlainText -Force
```

Wrap the username + secure password into a PSCredential object

```ruby
$Cred = New-Object System.Management.Automation.PSCredential('INLANEFREIGHT\backupadm', $SecPassword)
```


```ruby
get-domainuser -spn -credential $Cred | select samaccountname
```


---

## Register PSSession Configuration

This only works from a Windows host with GUI access — not evil-winrm

```ruby
Register-PSSessionConfiguration -Name backupadmsess -RunAsCredential inlanefreight\backupadm
```

```ruby
Restart-Service WinRM
```

Reconnect using the named configuration

```ruby
Enter-PSSession -ComputerName DEV01 -Credential INLANEFREIGHT\backupadm -ConfigurationName backupadmsess
```

```ruby
get-domainuser -spn | select samaccountname
```


**Workaround 1 — PSCredential** is your go-to when you're inside an evil-winrm shell. It's simple, requires no setup, but is annoying because you must add `-credential $Cred` to _every single command_. Some tools don't even support that flag.

**Workaround 2 — PSSession config** is the cleaner solution — it fixes the problem at the session level so every command works normally. The catch is it requires a proper Windows PowerShell terminal with GUI access (a popup appears to confirm the password), so you can't use it from evil-winrm or from a Linux attack host. Best used when you've RDP'd into a jump host inside the network.