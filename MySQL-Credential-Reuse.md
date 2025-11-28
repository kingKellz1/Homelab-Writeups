# Lab 5: Credential Reuse on MySQL Database

**Target:** Metasploitable 2 VM
**IP Address:** 192.168.10.100
**Service:** MySQL (Port 3306)
**Tool:** mysql client

---

## 1. Authentication Check

Initial attempts to log in with `msfadmin/msfadmin` failed, revealing that the MySQL user `msfadmin` was configured to deny remote connections from the Kali IP address (`192.168.10.128`).

This required pivoting to common **default database credentials**.

**Successful Login Command:**
```bash
mysql -h 192.168.10.100 -u root -p --skip-ssl

Flag Used: --skip-ssl was required to bypass handshake errors with the older MySQL version.

Credentials Used: root / [blank password] (ENTER key only)

Result: Successful login to the mysql > prompt, granting administrative privileges.

---

## 2. Exploitation: Database Enumeration

Full access to the database allows for complete information disclosure.

Commands Executed (Proof of Access):

SHOW DATABASES;
USE dvwa;
SHOW TABLES;
SELECT * FROM users;

Impact: The attacker gained full read/write access to all databases and tables on the system, including sensitive application data (dvwa user tables).

---

## 3. Mitigation/Remediation

The primary flaw was the use of default vendor settings for a high-privilege account.

Primary Fix: Immediately assign a strong, complex password to the root database account.

Access Control: Restrict the root account to only be accessible from localhost or from specific, trusted administrative IP addresses.

Encryption: Configure the MySQL server and client to use modern TLS/SSL encryption for all remote traffic to prevent sniffing of credentials and data.
