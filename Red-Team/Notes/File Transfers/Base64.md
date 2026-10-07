


As a basic uploading , i can convert file content to base64 and then copy paste to my attacker machine(target machine) then decode it.

#Encoding

 (Windows):
```cmd
certutil -encode input.txt output.txt
```

```powershell
[Convert]::ToBase64String([IO.File]::ReadAllBytes("input.txt")) > output.txt
```

 (Linux):
```bash
base64 [FILENAME]
```



#Decoding
 (Windows):
```cmd
certutil -decode input.txt output.txt
```

```powershell
[IO.File]::WriteAllBytes("output.txt",[Convert]::FromBase64String((Get-Content "input.txt")))
```


Linux 
```bash
cat [FILENAME] | base64 -d
```