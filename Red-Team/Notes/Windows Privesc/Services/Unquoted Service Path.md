
**Concept**: if a service's binary path contains spaces and isn't wrapped in quotes, Windows tries each space-delimited segment as a potential executable, in order, before reaching the real one. If you can write to one of those earlier path segments, you win — but per the doc, this is _rarely actually exploitable_ since writing to `C:\` or `C:\Program Files` normally needs admin rights anyway.

**Example vulnerable path**

```
C:\Program Files (x86)\System Explorer\service\SystemExplorerService64.exe
```

Windows tries, in order:

```
C:\Program.exe
C:\Program Files.exe
C:\Program Files (x86)\System.exe
C:\Program Files (x86)\System Explorer\service\SystemExplorerService64.exe
```

**Query a specific service**

```
sc qc SystemExplorerHelpService
```

**Hunt for all unquoted auto-start services**

```
wmic service get name,displayname,pathname,startmode |findstr /i "auto" | findstr /i /v "c:\windows\\" | findstr /i /v """
```

