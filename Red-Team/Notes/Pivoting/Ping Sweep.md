
#### Ping Sweep For Loop on Linux Pivot Hosts

```ruby
for i in {1..254} ;do (ping -c 1 172.16.5.$i | grep "bytes from" &) ;done
```


#### Ping Sweep For Loop Using CMD

```ruby
for /L %i in (1,1,254) do @ping -n 1 -w 200 172.16.6.%i | findstr "Reply from"
```


#### Ping Sweep Using PowerShell

```ruby
1..254 | % {"172.16.5.$($_): $(Test-Connection -count 1 -comp 172.16.5.$($_) -quiet)"}
```
