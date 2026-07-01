# Metasploitable 2 - Enumeration Report

> **Target Machine:** Metasploitable 2  
> **Platform:** TryHackMe / Hack The Box / Local Lab  
> **Purpose:** Initial Enumeration & Reconnaissance  
> **Status:** Enumeration Complete

---

# Target Information

| Item | Value |
|------|-------|
| Target IP | `192.168.245.132` |
| Scanner | Nmap 7.99 |
| Operating System | Ubuntu Linux |
| Hostname | `metasploitable.localdomain` |
| NetBIOS Name | `METASPLOITABLE` |
| Workgroup | `WORKGROUP` |
| MAC Address | `00:0C:29:FA:DD:2A` |
| Virtualization | VMware |

---

# Scan Commands

## Basic Scan

```bash
nmap 192.168.245.132
```

---

## Service Version Detection

```bash
nmap -sV 192.168.245.132
```

---

## Full Enumeration

```bash
nmap -sV -sC -p- 192.168.245.132
```

---

# Open Ports Summary

| Port | Protocol | Service | Version |
|-------|----------|----------|----------|
|21|TCP|FTP|vsftpd 2.3.4|
|22|TCP|SSH|OpenSSH 4.7p1|
|23|TCP|Telnet|Linux telnetd|
|25|TCP|SMTP|Postfix|
|53|TCP|DNS|ISC BIND 9.4.2|
|80|TCP|HTTP|Apache 2.2.8|
|111|TCP|RPCBind|RPC v2|
|139|TCP|NetBIOS|Samba|
|445|TCP|SMB|Samba 3.0.20|
|512|TCP|rexecd|netkit-rsh|
|513|TCP|rlogin|OpenBSD/Solaris|
|514|TCP|Shell|tcpwrapped|
|1099|TCP|Java RMI|GNU Classpath|
|1524|TCP|Bind Shell|Metasploitable Root Shell|
|2049|TCP|NFS|RPC|
|2121|TCP|FTP|ProFTPD 1.3.1|
|3306|TCP|MySQL|5.0.51|
|3632|TCP|distccd|v1|
|5432|TCP|PostgreSQL|8.3|
|5900|TCP|VNC|Protocol 3.3|
|6000|TCP|X11|Access Denied|
|6667|TCP|IRC|UnrealIRCd|
|6697|TCP|IRC SSL|UnrealIRCd|
|8009|TCP|AJP13|Apache JServ|
|8180|TCP|HTTP|Apache Tomcat 5.5|
|8787|TCP|Ruby DRb|Ruby 1.8|
|34034|TCP|mountd|RPC|
|37862|TCP|Java RMI|GNU Classpath|
|45026|TCP|status|RPC|
|58729|TCP|nlockmgr|RPC|

---

# Detailed Service Enumeration

---

## FTP (21)

### Version

```
vsftpd 2.3.4
```

### Findings

- Anonymous login enabled
- Plaintext communication
- vsFTPd 2.3.4 (Known vulnerable version)

### Nmap Evidence

```
Anonymous FTP login allowed
```

### Enumeration Commands

```bash
ftp 192.168.245.132

Username: anonymous
Password:
```

```bash
nmap --script ftp-anon 192.168.245.132
```

Potential vulnerabilities:

- Anonymous Information Disclosure
- VSFTPD Backdoor (Version Specific)

---

## SSH (22)

### Version

```
OpenSSH 4.7p1
```

### Findings

- SSH available
- Older OpenSSH version

### Enumeration

```bash
ssh root@192.168.245.132
```

---

## Telnet (23)

### Findings

- Plaintext authentication
- Possible weak credentials

Enumeration

```bash
telnet 192.168.245.132
```

---

## SMTP (25)

### Version

```
Postfix SMTP
```

Supported commands

- VRFY
- STARTTLS
- PIPELINING
- ETRN

Enumeration

```bash
smtp-user-enum
```

```bash
nmap --script smtp-enum-users
```

Possible usernames

```
root
msfadmin
postgres
mysql
user
service
daemon
```

---

## DNS (53)

### Version

```
ISC BIND 9.4.2
```

Enumeration

```bash
dig axfr @192.168.245.132
```

```bash
dnsrecon
```

---

## HTTP (80)

### Version

```
Apache 2.2.8
```

Title

```
Metasploitable2 - Linux
```

Enumeration

```bash
nikto
```

```bash
gobuster dir
```

```bash
whatweb
```

```bash
curl
```

Potential vulnerabilities

- Old Apache
- WebDAV
- Default pages

---

## SMB (139/445)

### Version

```
Samba 3.0.20
```

Findings

- Guest access
- SMB Signing Disabled

Enumeration

```bash
smbclient -L //192.168.245.132
```

```bash
enum4linux
```

```bash
smbmap
```

Potential Vulnerabilities

- Samba username map script
- Null Sessions
- Share Enumeration

---

## RPC (111)

Services

- NFS
- mountd
- nlockmgr

Enumeration

```bash
rpcinfo -p
```

---

## NFS (2049)

Enumeration

```bash
showmount -e 192.168.245.132
```

```bash
mount
```

Potential

- Exported directories
- Sensitive files

---

## R Services

Ports

```
512
513
514
```

Protocols

- rexec
- rlogin
- rsh

Very old insecure services.

---

## Java RMI (1099)

Enumeration

```bash
nmap --script rmi-dumpregistry
```

Possible exploitation

- Java Deserialization

---

## Root Bind Shell (1524)

Service

```
Metasploitable Root Shell
```

High Priority Target

Connect

```bash
nc 192.168.245.132 1524
```

Expected Result

```
Root shell
```

---

## FTP (2121)

Version

```
ProFTPD 1.3.1
```

Enumeration

```bash
ftp
```

Potential vulnerabilities

- Mod_Copy
- Weak Credentials

---

## MySQL (3306)

Version

```
5.0.51
```

Enumeration

```bash
mysql -h 192.168.245.132
```

Try

```
root
root

root
password

msfadmin
msfadmin
```

---

## distccd (3632)

Version

```
GNU distccd
```

Known Vulnerability

Remote Command Execution

Enumeration

```bash
searchsploit distccd
```

---

## PostgreSQL (5432)

Version

```
8.3
```

Enumeration

```bash
psql
```

Default Credentials

```
postgres
postgres
```

---

## VNC (5900)

Protocol

```
3.3
```

Enumeration

```bash
vncviewer
```

---

## X11 (6000)

Findings

```
Access Denied
```

---

## UnrealIRCd (6667/6697)

Known Vulnerability

```
Backdoor Command Execution
```

Enumeration

```bash
nc
```

```bash
searchsploit unrealircd
```

---

## Apache JServ (8009)

Enumeration

```bash
ajp-cli
```

Potential

- Ghostcat
- File Read

---

## Apache Tomcat (8180)

Version

```
Tomcat 5.5
```

Enumeration

```bash
nikto
```

```bash
curl
```

Check

```
/manager/html
```

Try default credentials.

---

## Ruby DRb (8787)

Ruby Distributed Objects

Potential

- Remote Ruby Execution

---

# Host Script Results

## SMB

- Guest Login Allowed
- SMB Signing Disabled

---

## SSL

Certificate

Expired

```
2010
```

SSLv2 Supported

Weak Cipher Suites

---

## Clock Skew

```
Approximately 5 seconds
```

---

# Initial Attack Surface

| Priority | Service | Reason |
|----------|----------|---------|
|⭐⭐⭐⭐⭐|1524|Root Shell already exposed|
|⭐⭐⭐⭐⭐|vsFTPD|Known backdoor|
|⭐⭐⭐⭐⭐|UnrealIRCd|Backdoor RCE|
|⭐⭐⭐⭐⭐|distccd|RCE|
|⭐⭐⭐⭐|Tomcat|Weak Credentials|
|⭐⭐⭐⭐|Samba|Username Map Script|
|⭐⭐⭐⭐|NFS|File Disclosure|
|⭐⭐⭐|MySQL|Weak Credentials|
|⭐⭐⭐|PostgreSQL|Weak Credentials|
|⭐⭐⭐|SMTP|User Enumeration|
|⭐⭐|Telnet|Weak Passwords|

---

# Recommended Enumeration Tools

```bash
nikto
```

```bash
gobuster
```

```bash
enum4linux
```

```bash
smbmap
```

```bash
showmount
```

```bash
rpcinfo
```

```bash
searchsploit
```

```bash
hydra
```

```bash
whatweb
```

```bash
wpscan
```

```bash
dirsearch
```

```bash
nuclei
```

---

# SearchSploit Targets

```bash
searchsploit vsftpd 2.3.4
```

```bash
searchsploit unrealircd
```

```bash
searchsploit samba 3.0.20
```

```bash
searchsploit distccd
```

```bash
searchsploit tomcat 5.5
```

```bash
searchsploit proftpd 1.3.1
```

---

# Potential CVEs

| Service | Example CVEs |
|----------|--------------|
|vsFTPD|CVE-2011-2523|
|UnrealIRCd|CVE-2010-2075|
|distccd|CVE-2004-2687|
|Samba|CVE-2007-2447|
|Tomcat|Multiple legacy vulnerabilities|
|BIND|Multiple 9.4.x vulnerabilities|

---

# Enumeration Completed

✅ Host Discovery

✅ Port Scan

✅ Version Detection

✅ Default Script Scan

✅ Full TCP Scan

---

# Next Phase

- FTP Enumeration
- SMB Enumeration
- Web Enumeration
- NFS Enumeration
- Database Enumeration
- Exploitation
- Privilege Escalation
- Post Exploitation

---

## Disclaimer

This report was generated against **Metasploitable 2**, an intentionally vulnerable Linux virtual machine designed for security training and penetration testing in authorized lab environments only.
