
```ruby
Import-Module .\PowerView.ps1
```

```ruby
$sid = Convert-NameToSid wley
```

#### Using Get-DomainObjectACL

```ruby
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $sid} 
```

PowerView has the `ResolveGUIDs` flag, which does this very thing for us. Notice how the output changes when we include this flag to show the human-readable format of the `ObjectAceType` property as `User-Force-Change-Password`.

#### Further Enumeration of Rights Using damundsen (another user)

```ruby
$sid2 = Convert-NameToSid damundsen
```

```ruby
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $sid2}
```


### Investigating the Help Desk Level 1 Group with Get-DomainGroup

```ruby
Get-DomainGroup -Identity "Help Desk Level 1" | select memberof
```


