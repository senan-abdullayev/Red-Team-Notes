

```ruby
Telnet <Ip> 25 
```

### User enumeration

To verify users commands are : 

```ruby
VRFY root
```

### USER Command

```ruby
Telnet <IP> 110
```

we can use the command `USER` followed by the username, and if the server responds `OK`. This means that the user exists on the server.

```ruby
USER julio
```

### To automate our enumeration process, we can use a tool named smtp-user-enum:

```ruby
smtp-user-enum -M RCPT -U userlist.txt -D inlanefreight.htb -t 10.129.203.7
```

