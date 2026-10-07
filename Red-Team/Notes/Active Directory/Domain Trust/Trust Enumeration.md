

```ruby
Import-Module activedirectory
```

#### Get-DomainTrustMapping


```ruby
Get-DomainTrustMapping
```

From here, we could begin performing enumeration across the trusts. For example, we could look at all users in the child domain:

```ruby
Get-DomainUser -Domain LOGISTICS.INLANEFREIGHT.LOCAL | select SamAccountName
```

#### Using netdom to query domain trust

```ruby
netdom query /domain:inlanefreight.local trust
```

We can also use BloodHound to visualize these trust relationships by using the `Map Domain Trusts` pre-built query.
