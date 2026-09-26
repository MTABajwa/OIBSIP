# Port Analysis Report — Metasploitable 2

## Overview
This document provides a detailed security analysis of the 23 open TCP ports discovered during the Nmap scan of the Metasploitable 2 target (`192.168.109.129`). For each port, the running service, risk level, potential impact, and recommended mitigation strategies are documented.

---

## Port 21/TCP — FTP (vsftpd 2.3.4)

**What it does**: File Transfer Protocol used for uploading and downloading files.

**Risk Level**: CRITICAL

**Why it's risky**: vsftpd 2.3.4 contains a known backdoor (CVE-2011-2523). An attacker can trigger a root shell by sending a username ending in `:)` (smiley face). This allows unauthenticated remote code execution.

**Mitigation**: Upgrade vsftpd to the latest version. Disable anonymous FTP access. Use SFTP instead of FTP. Block port 21 at the firewall perimeter.

---

## Port 22/TCP — SSH (OpenSSH 4.7p1 Debian)

**What it does**: Secure Shell for encrypted remote login and file transfer.

**Risk Level**: MEDIUM

**Why it's risky**: This is an outdated version of OpenSSH with known vulnerabilities. It is also susceptible to brute-force and dictionary attacks targeting weak credentials.

**Mitigation**: Upgrade OpenSSH to the latest version. Disable password authentication and use SSH keys only. Implement fail2ban to block repeated failed login attempts. Restrict SSH access by IP address.

---

## Port 23/TCP — Telnet (Linux telnetd)

**What it does**: Unencrypted remote login service.

**Risk Level**: HIGH

**Why it's risky**: Telnet transmits all data, including usernames and passwords, in plaintext. An attacker on the same network can easily intercept credentials using a packet sniffer like Wireshark.

**Mitigation**: Disable Telnet completely. Replace with SSH for all remote management. Block port 23 at the firewall.

---

## Port 25/TCP — SMTP (Postfix smtpd)

**What it does**: Simple Mail Transfer Protocol for sending and receiving email.

**Risk Level**: MEDIUM

**Why it's risky**: If misconfigured, SMTP can be used as an open relay to send spam. Attackers can also enumerate valid users on the system.

**Mitigation**: Configure SMTP authentication. Disable open relay. Implement rate limiting. Use TLS for email transmission.

---

## Port 53/TCP — DNS (ISC BIND 9.4.2)

**What it does**: Domain Name System server for resolving domain names to IP addresses.

**Risk Level**: HIGH

**Why it's risky**: This is an old version of BIND with known vulnerabilities (e.g., cache poisoning). If DNS zone transfers are allowed, attackers can map the entire network.

**Mitigation**: Upgrade BIND to the latest version. Restrict zone transfers to authorized secondary DNS servers only. Implement DNSSEC. Bind DNS to specific interfaces.

---

## Port 80/TCP — HTTP (Apache httpd 2.2.8)

**What it does**: Web server hosting multiple vulnerable applications (DVWA, phpMyAdmin, Mutillidae).

**Risk Level**: HIGH

**Why it's risky**: The Apache version is outdated and vulnerable to multiple CVEs. The hosted web applications contain severe vulnerabilities including SQL injection, cross-site scripting (XSS), and remote command execution.

**Mitigation**: Upgrade Apache to the latest version. Remove unnecessary web applications. Implement a Web Application Firewall (WAF). Enforce HTTPS with valid TLS certificates.

---

## Port 111/TCP — rpcbind (RPC #100000)

**What it does**: Maps Remote Procedure Call (RPC) program numbers to network ports.

**Risk Level**: LOW to MEDIUM

**Why it's risky**: RPCbind can be used for information gathering, allowing attackers to enumerate RPC services and potentially launch amplification attacks (DDoS).

**Mitigation**: Restrict access to port 111 using firewall rules. Disable RPCbind if not required. Keep RPC services patched.

---

## Port 139/TCP & 445/TCP — SMB (Samba smbd 3.X - 4.X)

**What it does**: Server Message Block for file and printer sharing.

**Risk Level**: CRITICAL

**Why it's risky**: These ports expose the system to EternalBlue (CVE-2017-0144) and WannaCry-style ransomware attacks. SMB is one of the most targeted protocols in enterprise networks.

**Mitigation**: Immediately block ports 139 and 445 at the network perimeter. Disable SMBv1. Enable SMB signing and encryption. Apply security patches to Samba.

---

## Ports 512, 513, 514/TCP — R-services (rexec, rlogin, rsh)

**What it does**: Legacy remote execution and login services.

**Risk Level**: HIGH

**Why it's risky**: These services transmit data in plaintext and rely on weak trust-based authentication (e.g., `.rhosts` files). They are trivially exploitable.

**Mitigation**: Disable all R-services completely. Replace with SSH. Block ports 512-514 at the firewall.

---

## Port 1099/TCP — Java RMI (GNU Classpath grmiregistry)

**What it does**: Java Remote Method Invocation registry for distributed Java applications.

**Risk Level**: HIGH

**Why it's risky**: Java RMI is vulnerable to deserialization attacks, which can lead to remote code execution. This is a common attack vector in Java-based environments.

**Mitigation**: Restrict access to port 1099. Use RMI over SSL/TLS. Avoid deserializing untrusted data. Apply the latest Java security patches.

---

## Port 1524/TCP — Ingreslock (Metasploitable root shell)

**What it does**: A deliberately planted backdoor root shell.

**Risk Level**: CRITICAL

**Why it's risky**: This port provides an immediate, unauthenticated root shell to anyone who connects. It is the most severe finding in this scan.

**Mitigation**: Remove the backdoor immediately. Reinstall the operating system if the system has been compromised. Block port 1524 at the firewall.

---

## Port 2049/TCP — NFS (Network File System)

**What it does**: Allows remote file systems to be mounted and accessed over the network.

**Risk Level**: HIGH

**Why it's risky**: If NFS exports are misconfigured, attackers can mount file systems and read or modify sensitive data. NFS often lacks strong authentication.

**Mitigation**: Restrict NFS exports to specific IP addresses. Use Kerberos for NFS authentication. Disable NFS if not required. Block port 2049 externally.

---

## Port 2121/TCP — FTP (ProFTPD 1.3.1)

**What it does**: Another FTP server running on a non-standard port.

**Risk Level**: HIGH

**Why it's risky**: ProFTPD 1.3.1 contains multiple known vulnerabilities, including a remote code execution flaw. Running FTP on a non-standard port does not improve security.

**Mitigation**: Upgrade ProFTPD. Disable FTP entirely and use SFTP. Block port 2121.

---

## Port 3306/TCP — MySQL (5.0.51a)

**What it does**: Database server for storing and retrieving data.

**Risk Level**: HIGH

**Why it's risky**: This is an extremely old version of MySQL. It often ships with weak or default credentials. If exposed, attackers can extract, modify, or delete sensitive data.

**Mitigation**: Upgrade MySQL. Bind MySQL to `127.0.0.1` (localhost) only. Use strong, unique passwords. Restrict network access. Enable audit logging.

---

## Port 5432/TCP — PostgreSQL (8.3.0 - 8.3.7)

**What it does**: Database server for storing and retrieving data.

**Risk Level**: HIGH

**Why it's risky**: Outdated PostgreSQL version with known vulnerabilities. Like MySQL, it may use weak default credentials and exposes sensitive data if accessible remotely.

**Mitigation**: Upgrade PostgreSQL. Bind to localhost only. Use strong passwords. Restrict access by IP. Enable SSL for database connections.

---

## Port 5900/TCP — VNC (Protocol 3.3)

**What it does**: Virtual Network Computing for remote desktop access.

**Risk Level**: HIGH

**Why it's risky**: VNC protocol 3.3 uses weak encryption (or none at all). Passwords are often short and easily brute-forced. If compromised, attackers gain full graphical control of the system.

**Mitigation**: Use VNC only over SSH tunnels. Set strong, complex VNC passwords. Upgrade to a newer VNC version with better encryption. Restrict access by IP.

---

## Port 6000/TCP — X11

**What it does**: X Window System for graphical display.

**Risk Level**: MEDIUM

**Why it's risky**: If X11 is misconfigured, attackers can capture keystrokes, take screenshots, and interact with the graphical session remotely. The scan noted "access denied," which is a good sign, but the service is still exposed.

**Mitigation**: Disable X11 forwarding if not needed. Use SSH X11 forwarding with trusted hosts only. Block port 6000 at the firewall.

---

## Port 6667/TCP — IRC (UnrealIRCd)

**What it does**: Internet Relay Chat server.

**Risk Level**: CRITICAL

**Why it's risky**: The version of UnrealIRCd running on Metasploitable 2 contains a known backdoor (CVE-2010-2075) that allows remote command execution. Attackers can run arbitrary commands as the IRC daemon user.

**Mitigation**: Remove UnrealIRCd entirely or upgrade to a patched version. Block port 6667 at the firewall.

---

## Port 8009/TCP — AJP13 (Apache Jserv Protocol)

**What it does**: Apache JServ Protocol, used for communication between a web server and Tomcat.

**Risk Level**: MEDIUM

**Why it's risky**: AJP13 is vulnerable to the Ghostcat vulnerability (CVE-2020-1938), which allows file read and potentially remote code execution if Tomcat is misconfigured.

**Mitigation**: Disable AJP if not required. Bind AJP to localhost only. Upgrade Tomcat. Block port 8009 externally.

---

## Port 8180/TCP — HTTP (Apache Tomcat/Coyote JSP engine 1.1)

**What it does**: Tomcat web server management interface.

**Risk Level**: HIGH

**Why it's risky**: Tomcat manager applications often use default credentials (`tomcat:tomcat`). If compromised, attackers can deploy malicious WAR files to achieve remote code execution.

**Mitigation**: Remove default applications. Change default credentials immediately. Restrict access to the manager interface. Upgrade Tomcat to the latest version.

---

## Summary Table

| Port | Service | Version | Risk Level | Primary Concern |
|------|---------|---------|------------|-----------------|
| 21 | FTP | vsftpd 2.3.4 | CRITICAL | Backdoor allows root access |
| 22 | SSH | OpenSSH 4.7p1 | MEDIUM | Brute-force, outdated version |
| 23 | Telnet | Linux telnetd | HIGH | Plaintext credentials |
| 25 | SMTP | Postfix smtpd | MEDIUM | Open relay, user enumeration |
| 53 | DNS | ISC BIND 9.4.2 | HIGH | Cache poisoning, zone transfers |
| 80 | HTTP | Apache 2.2.8 | HIGH | Web app vulnerabilities |
| 111 | rpcbind | RPC #100000 | LOW-MEDIUM | Information disclosure |
| 139/445 | SMB | Samba 3.X - 4.X | CRITICAL | EternalBlue / ransomware |
| 512-514 | R-services | rexec, rlogin, rsh | HIGH | Plaintext, trust-based auth |
| 1099 | Java RMI | GNU Classpath | HIGH | Deserialization RCE |
| 1524 | Ingreslock | Metasploitable root shell | CRITICAL | Unauthenticated root access |
| 2049 | NFS | Network File System | HIGH | Unauthorized file access |
| 2121 | FTP | ProFTPD 1.3.1 | HIGH | Remote code execution |
| 3306 | MySQL | 5.0.51a | HIGH | Data breach, weak credentials |
| 5432 | PostgreSQL | 8.3.0 - 8.3.7 | HIGH | Data breach, weak credentials |
| 5900 | VNC | Protocol 3.3 | HIGH | Weak encryption, remote desktop |
| 6000 | X11 | X Window System | MEDIUM | Keystroke capture, screen access |
| 6667 | IRC | UnrealIRCd | CRITICAL | Backdoor for remote command execution |
| 8009 | AJP13 | Apache Jserv | MEDIUM | Ghostcat (CVE-2020-1938) |
| 8180 | HTTP | Tomcat 1.1 | HIGH | Default credentials, WAR deployment |

---

## Overall Assessment

The Nmap scan of Metasploitable 2 revealed **23 open TCP ports**, exposing a massive and highly vulnerable attack surface. In a real-world production environment, this system would be considered **critically compromised** within minutes of being exposed to a network.

**Key Critical Findings:**
1. **vsftpd 2.3.4 Backdoor (Port 21)** — Allows unauthenticated root access.
2. **SMB (Ports 139/445)** — Vulnerable to EternalBlue and WannaCry ransomware.
3. **Ingreslock Root Shell (Port 1524)** — Provides immediate root access with no authentication.
4. **UnrealIRCd Backdoor (Port 6667)** — Allows remote command execution.

**Immediate Remediation Priorities:**
1. Isolate the system from the network immediately.
2. Remove or patch the vsftpd, UnrealIRCd, and Samba services.
3. Close the Ingreslock backdoor port (1524).
4. Disable Telnet and all R-services in favor of SSH.
5. Rebuild the system from a trusted image if any of these services were exposed to an untrusted network.

This exercise demonstrates the critical importance of regular vulnerability scanning, patch management, and minimizing the attack surface by disabling unnecessary services.
