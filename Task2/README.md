Task 2 — Vulnerability Assessment & Web Application Testing

Objective
Perform vulnerability assessment and web application testing on Metasploitable 2 using industry-standard tools. Identify common web vulnerabilities including SQL Injection, XSS, and insecure headers.

Target
- **IP:** 192.168.118.129 (Metasploitable 2 — Local Lab VM)
- **Web App:** DVWA (Damn Vulnerable Web Application)
- **Environment:** Kali Linux on VMware

Tools Used
| Tool | Purpose |
|------|---------|
| Nikto | Automated web vulnerability scanner |
| DVWA | Practice web app for manual exploit testing |
| curl | HTTP header inspection |
| Browser | Manual testing of SQLi and XSS |

Vulnerabilities Found

1. Nikto Scan — 27 Issues Found
- Apache/2.2.8 outdated (EOL version)
- PHP/5.2.4 version exposed via X-Powered-By header
- phpinfo.php publicly accessible — leaks full system info
- phpMyAdmin exposed — database admin panel accessible
- HTTP TRACE method enabled — vulnerable to XST attacks
- X-Frame-Options header missing — clickjacking risk
- X-Content-Type-Options header missing — MIME sniffing risk
- Directory indexing enabled on /doc/, /icons/, /test/
- wp-config.php file found — may contain credentials

2. SQL Injection (DVWA — Low Security)
- **Payload used:** `1' OR '1'='1`
- **Result:** Successfully dumped all user records from the database
- **Risk:** Critical — attacker can read, modify, or delete entire database

3. Cross-Site Scripting — XSS (DVWA — Low Security)
- **Payload used:** `<script>alert('XSS')</script>`
- **Result:** JavaScript executed successfully in browser
- **Risk:** High — attacker can steal session cookies, redirect users

4. Insecure HTTP Headers (curl -I)
| Missing Header | Risk |
|---|---|
| X-Frame-Options | Clickjacking attacks |
| X-Content-Type-Options | MIME sniffing |
| X-XSS-Protection | Browser XSS filter disabled |
| Content-Security-Policy | No script restriction |
| Server version exposed | Apache/2.2.8 info leaked |
| PHP version exposed | PHP/5.2.4 info leaked |

Commands Used
```bash
nikto -h http://192.168.118.129
curl -I http://192.168.118.129
```

Mitigation Recommendations
- Upgrade Apache to 2.4.x and PHP to 8.x
- Remove phpinfo.php from production
- Restrict phpMyAdmin to authorized IPs only
- Disable HTTP TRACE method
- Add security headers: X-Frame-Options, CSP, X-Content-Type-Options
- Use prepared statements to prevent SQL Injection
- Sanitize all user inputs to prevent XSS
- Set DVWA security to High in production environments

Screenshots
See /Task2/screenshots/ folder

Conclusion
Task 2 demonstrated automated vulnerability scanning using Nikto and manual web application testing on DVWA. Critical vulnerabilities including SQL Injection and XSS were successfully exploited in the controlled lab environment, confirming the importance of input validation and security headers in web applications.
