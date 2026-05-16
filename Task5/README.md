Task 5 — Final Security Audit & Penetration Testing Report

Objective
Perform a complete security assessment on the Metasploitable 2 lab environment, combining all techniques from Tasks 1 to 4 into a single professional penetration testing report.

Target Details
| Field | Value |
|---|---|
| Target IP | 192.168.118.129 |
| Hostname | metasploitable.localdomain |
| OS | Linux 2.6.x (Ubuntu 8.04) |
| Environment | VMware lab — authorized testing only |
| Tester | Haaris |
| Date | 16 May 2026 |

---

Methodology
I followed the standard penetration testing phases:

1. **Reconnaissance** — WHOIS, nslookup, theHarvester, recon-ng
2. **Scanning** — Nmap host discovery, port scan, service detection, OS detection
3. **Vulnerability Assessment** — Nikto web scan, Nmap vuln scripts, manual testing
4. **Exploitation** — SQL injection, XSS, Hydra brute force, backdoor detection
5. **Post Exploitation** — Identified root-level access via vsftpd backdoor
6. **Reporting** — This document

---

Attack Surface Analysis

Network Layer
- 23 open TCP ports discovered
- Multiple outdated and unpatched services running
- No firewall rules in place — all ports directly accessible
- SMB signing disabled — network traffic vulnerable to interception

Web Application Layer
- Apache 2.2.8 — end of life, no security patches
- PHP 5.2.4 — severely outdated
- DVWA accessible with default credentials (admin/password)
- SQL Injection and XSS confirmed on DVWA
- phpMyAdmin exposed with no access restrictions
- phpinfo.php publicly accessible — leaks full server configuration

Authentication Layer
- FTP anonymous login enabled
- Default credentials working on multiple services
- No account lockout on any service
- Telnet running — passwords transmitted in plain text

---

Critical Findings

Finding 1 — vsftpd 2.3.4 Backdoor (CVE-2011-2523)
| Field | Detail |
|---|---|
| Port | 21/tcp (FTP) |
| CVE | CVE-2011-2523 |
| Severity | Critical |
| Status | Confirmed Exploitable |

This version of vsftpd contains a malicious backdoor introduced into the source code in 2011. When a username containing :) is sent, the backdoor opens a shell on port 6200.

Nmap script confirmed the backdoor and executed `id` command which returned:
```
uid=0(root) gid=0(root)
```
This means complete root-level access to the system was achieved through FTP alone.

**Fix:** Immediately remove vsftpd 2.3.4 and upgrade to latest stable version. Disable anonymous FTP.

---

Finding 2 — UnrealIRCd Backdoor (CVE-2010-2075)
| Field | Detail |
|---|---|
| Port | 6667/tcp (IRC) |
|CVE | CVE-2010-2075 |
| Severity | Critical |
| Status | Confirmed |

UnrealIRCd 3.2.8.1 contains a backdoor that allows remote code execution by sending a specially crafted string to the IRC service.

**Fix:** Remove UnrealIRCd entirely or upgrade to a clean version.

---

Finding 3 — Metasploitable Root Shell (Port 1524)
| Field | Detail |
|---|---|
| Port | 1524/tcp |
| Severity | Critical |
| Status | Open and accessible |

Port 1524 is a classic intentional backdoor on Metasploitable — connecting to it directly gives a root shell with zero authentication required.

**Fix:** This port and service should never exist on any real system. Block immediately.

---

Finding 4 — SQL Injection on DVWA
| Field | Detail |
|---|---|
| URL | http://192.168.118.129/dvwa/vulnerabilities/sqli/ |
| Payload | `1' OR '1'='1` |
| Severity | Critical |
| Result | Full user table dumped from database |

**Fix:** Use prepared statements and parameterized queries. Never trust user input.

---

Finding 5 — Cross Site Scripting (XSS) on DVWA
| Field | Detail |
|---|---|
| URL | http://192.168.118.129/dvwa/vulnerabilities/xss_r/ |
| Payload | `<script>alert('XSS')</script>` |
| Severity | High |
| Result | JavaScript executed in browser |

**Fix:** Sanitize and encode all user input. Implement Content Security Policy headers.

---

Finding 6 — FTP Credentials Brute Forced (Hydra)
| Field | Detail |
|---|---|
| Port | 21/tcp |
| Tool | Hydra |
| Result | msfadmin:msfadmin — valid credentials found |
| Severity | High |

**Fix:** Disable FTP. Use SFTP with key-based authentication instead.

---

Finding 7 — Missing Security Headers
All of the following headers were missing from HTTP responses:
- X-Frame-Options
- X-Content-Type-Options
- Content-Security-Policy
- X-XSS-Protection

Server and PHP versions were exposed in response headers.

**Fix:** Add security headers via Apache configuration. Remove version disclosure.

---

Risk Summary

| Severity | Count |
|---|---|
| Critical | 4 |
| High | 8 |
| Medium | 6 |
| Low | 9 |
| **Total** | **27+** |

---

Tools Used (Complete List)
| Tool | Purpose |
|---|---|
| WHOIS / nslookup | Reconnaissance |
| theHarvester | OSINT gathering |
| recon-ng | Reconnaissance framework |
| Nmap | Port scanning, service detection, vuln scripts |
| Nikto | Web vulnerability scanning |
| DVWA | Web application exploit testing |
| Wireshark | Packet capture and traffic analysis |
| John the Ripper | Password hash cracking |
| Hydra | Brute force credential testing |
| curl | HTTP header inspection |

---

Recommendations

1. Take Metasploitable offline immediately — it should never be exposed on a real network
2. Patch all services to current supported versions
3. Disable Telnet, FTP, rsh, rlogin — replace with SSH and SFTP
4. Remove all backdoors — vsftpd 2.3.4, UnrealIRCd, port 1524
5. Enable firewall rules — block all unnecessary ports
6. Implement IDS/IPS to detect brute force and exploit attempts
7. Add all missing HTTP security headers
8. Enforce strong password policy and disable default credentials
9. Restrict phpMyAdmin and phpinfo.php from public access
10. Deploy SIEM for continuous log monitoring

---

Screenshots
See /Task5/ folder

Conclusion
This final audit confirmed that Metasploitable 2 is extremely vulnerable across every layer — network, web application, and authentication. Multiple critical CVEs were confirmed exploitable including two backdoors that give direct root access. All findings from Tasks 1 through 4 were combined into this report demonstrating a complete penetration testing workflow from reconnaissance all the way through to reporting and recommendations.

The most alarming finding was CVE-2011-2523 on port 21 — a single Nmap script was enough to confirm root-level command execution on the target, which in a real environment would mean complete system compromise.
