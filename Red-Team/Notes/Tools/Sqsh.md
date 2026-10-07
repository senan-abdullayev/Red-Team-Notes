**Note:** When we authenticate to MSSQL using `sqsh` we can use the parameters `-h` to disable headers and footers for a cleaner look.


When using Windows Authentication, we need to specify the domain name or the hostname of the target machine. If we don't specify a domain or hostname, it will assume SQL Authentication and authenticate against the users created in the SQL Server. Instead, if we define the domain or hostname, it will use Windows Authentication. If we are targetting a local account, we can use `SERVERNAME\\accountname` or `.\\accountname`. The full command would look like:

```ruby
sqsh -S 10.129.203.7 -U .\\julio -P 'MyPassword!' -h
```
