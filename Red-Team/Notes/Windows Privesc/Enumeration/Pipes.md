
#### Listing Named Pipes with Pipelist

```
pipelist.exe /accepteula
```


#### Listing Named Pipes with PowerShell

```
gci \\.\pipe\
```


After obtaining a listing of named pipes, we can use [Accesschk](https://docs.microsoft.com/en-us/sysinternals/downloads/accesschk) to enumerate the permissions assigned to a specific named pipe by reviewing the Discretionary Access List (DACL), which shows us who has the permissions to modify, write, read, or execute a resource. Let's take a look at the `LSASS` process. We can also review the DACLs of all named pipes using the command `.\accesschk.exe /accepteula \pipe\`.


#### Reviewing LSASS Named Pipe Permissions

```
accesschk64.exe /accepteula \\.\Pipe\lsass -v
```

