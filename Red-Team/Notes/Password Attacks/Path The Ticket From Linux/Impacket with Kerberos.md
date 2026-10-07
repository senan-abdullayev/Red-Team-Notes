
our attack host doesn't have a connection to the `KDC/Domain Controller`, and we can't use the Domain Controller for name resolution. To use Kerberos, we need to proxy our traffic via `MS01` with a tool such as [Chisel](https://github.com/jpillora/chisel) and [Proxychains](https://github.com/haad/proxychains) and edit the `/etc/hosts` file to hardcode IP addresses of the domain and the machines we want to attack.

1. Chisel server listening (on Attacker machine)
2. Chisel client connects (on Target machine)
3. below command
```
proxychains impacket-wmiexec dc01 -k
```



# Evil-Winrm with Kerberos

```
proxychains evil-winrm -i dc01 -r inlanefreight.htb
```

