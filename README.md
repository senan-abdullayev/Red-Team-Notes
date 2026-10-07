<div align="center">

# 🛡️ CPTS Red Team Notes

**A field-tested reference for offensive security engagements**

Methodology, attack chains, and command snippets collected while grinding through the
HTB **Certified Penetration Testing Specialist (CPTS)** track and beyond.

![Focus](https://img.shields.io/badge/Focus-Offensive_Security-red)
![AD](https://img.shields.io/badge/Active_Directory-Attack_Chains-blue)
![Pivoting](https://img.shields.io/badge/Network-Pivoting_&_Tunneling-green)
![Platform](https://img.shields.io/badge/Platform-Kali_Linux-557C94?logo=kalilinux&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-lightgrey)

</div>

---

> ⚠️ **For authorized testing and education only.** Everything here is intended for use in
> legal engagements, labs, and CTFs where you have explicit written permission. Don't be the
> reason someone writes a new cautionary blog post.

---

## 📖 About

These are my working notes from the CPTS methodology — organized the way a real engagement
actually unfolds, not as a wall of disconnected commands. Each section covers the *why*
alongside the *how*, with copy-paste-ready snippets and the gotchas that cost me hours so
they don't cost you.

If you're prepping for CPTS, running internal pentests, or just want a solid offensive
reference, pull up a chair.

---

## 🗺️ Contents

| # | Phase | What's Inside |
|---|-------|---------------|
| 01 | **Recon & Enumeration** | Host discovery, service/version scanning, DNS, SMB, LDAP |
| 02 | **Web Exploitation** | SQLi, XXE, SSTI, file upload bypass, LFI→RCE, command injection |
| 03 | **Initial Foothold** | Reverse shells, shell stabilization, credential spraying |
| 04 | **Network Pivoting & Tunneling** | `ligolo-ng` / `chisel` multi-hop setups, listener relays |
| 05 | **Active Directory Attacks** | Kerberoasting, AS-REP roasting, ACL abuse, DCSync, ADCS |
| 06 | **Linux Privilege Escalation** | SUID, sudo abuse, cron, capabilities, group escapes |
| 07 | **Windows Privilege Escalation** | Service abuse, token impersonation, scheduler abuse |
| 08 | **Post-Exploitation & Loot** | Credential pillaging, persistence, cross-forest trust abuse |
| 09 | **Reporting** | Structuring findings, severity, remediation notes |

---

## 🧰 Core Toolkit

The tools that show up again and again across engagements:

```
Enumeration    nmap · netexec · ldapsearch · enum4linux-ng · gobuster
AD             BloodHound · Impacket · Certipy · Responder · evil-winrm
Web            Burp Suite · ffuf · sqlmap
Pivoting       ligolo-ng · chisel
Post-ex        Metasploit · mimikatz · secretsdump
```

---

## 🔗 Recurring Attack Chains

A few end-to-end chains documented in detail inside:

- **ADCS abuse** — ESC1 / ESC8 certificate attacks → domain escalation
- **ACL abuse** — BloodHound-driven `GenericWrite`, `ForceChangePassword`,
  `WriteDACL`, Shadow Credentials
- **Coercion → relay** — LLMNR/Responder poisoning, Kerberoasting, AS-REP roasting
- **Double pivot** — `ligolo-ng` through a DMZ host to reach an internal DC
- **Credential recovery** — DPAPI, `ntds.dit` exfil, backup/snapshot pillaging
- **Domain takeover** — DCSync, Silver/Golden Ticket, cross-forest trust escalation

---

## 📂 Structure

```
cpts-red-team-notes/
├── 01-recon/
├── 02-web/
├── 03-foothold/
├── 04-pivoting/
├── 05-active-directory/
├── 06-linux-privesc/
├── 07-windows-privesc/
├── 08-post-exploitation/
├── 09-reporting/
└── README.md
```

---

## 🤝 Contributing

Spotted a mistake, a better one-liner, or a technique worth adding? PRs and issues welcome.
Keep snippets tested and add a line on *why* it works, not just what to run.

---

## 📜 License

Released under the [MIT License](LICENSE). Use them, fork them, learn from them.

---

<div align="center">

**Maintained by [Landau](https://github.com/senan-abdullayev)**
🔗 [GitHub](https://github.com/senan-abdullayev) · [LinkedIn](https://linkedin.com/in/senan-abdullayev-938877385)

*Happy hacking — stay in scope.* 🎯

</div>
