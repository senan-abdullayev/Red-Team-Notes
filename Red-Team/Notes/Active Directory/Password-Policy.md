
## Rpcclient


```ruby
rpcclient -U "" -N 172.16.5.5
```


```r
rpcclient $> getdompwinfo
```


## Ldapsearch

```ruby
ldapsearch -H 172.16.5.5 -x -b "DC=INLANEFREIGHT,DC=LOCAL" -s sub "*" | grep -m 1 -B 10 pwdHistoryLength
```


## net.exe

```ruby
net accounts
```

