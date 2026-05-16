Task 3 — Password Security, Sniffing & Session Analysis

Objective
Capture and analyze network traffic using Wireshark, perform password auditing using John the Ripper and Hydra, and demonstrate session security concepts.

Target
- **IP:** 192.168.118.129 (Metasploitable 2 — Local Lab VM)
- **Environment:** Kali Linux on VMware

Tools Used
| Tool | Purpose |
|------|---------|
| Wireshark | Packet capture and traffic analysis |
| John the Ripper | Offline password hash cracking |
| Hydra | Online brute force / credential testing |

Task 3.1 — Wireshark Packet Capture

Interface Used
- eth0 (primary network interface)

Traffic Analyzed
| Filter | Protocol | Finding |
|--------|---------|---------|
| http | HTTP | GET requests to Metasploitable web server captured |
| tcp | TCP | Full TCP handshake (SYN, SYN-ACK, ACK) visible |
| arp | ARP | ARP broadcast packets between hosts captured |
| dns | DNS | DNS queries for domain resolution captured |

Key Finding
HTTP traffic is transmitted in plain text — credentials and data are fully visible in Wireshark. This demonstrates why HTTPS is essential for all web communication.

Task 3.2 — John the Ripper (Password Hash Cracking)

Command Used
```bash
echo 'root:$1$MmkkIzM8$MtE3HCrspDvSMkXtHhJwB.:14747:0:99999:7:::' > ~/test_hash.txt
john ~/test_hash.txt
```

Finding
- Hash type detected: md5crypt ($1$ format)
- John successfully loaded the hash and began cracking using wordlist and incremental modes
- Demonstrates that weak MD5 password hashes can be cracked offline

Task 3.3 — Hydra Brute Force (FTP)

Command Used
```bash
hydra -l msfadmin -p msfadmin 192.168.118.129 ftp -V
```

Result
[21][ftp] host: 192.168.118.129   login: msfadmin   password: msfadmin
1 of 1 target successfully completed, 1 valid password found

Finding — Critical
FTP credentials cracked successfully. FTP transmits passwords in plain text and has no brute force protection — making it trivially attackable.

Session Hijacking — Concepts

Risks of Insecure Session Management
- Session IDs transmitted over HTTP can be captured via Wireshark
- Predictable session tokens can be guessed by attackers
- Missing HttpOnly and Secure cookie flags expose sessions to theft

Prevention Techniques
- Always use HTTPS to encrypt session tokens in transit
- Set HttpOnly and Secure flags on all cookies
- Implement session timeout and regenerate session ID after login
- Use unpredictable, cryptographically random session tokens

Mitigation Recommendations
- Replace FTP with SFTP or SCP for secure file transfer
- Use bcrypt or SHA-512 instead of MD5 for password hashing
- Implement account lockout after failed login attempts
- Use HTTPS for all web traffic to prevent packet sniffing
- Monitor network traffic for anomalous patterns using IDS/IPS

Screenshots
See /Task3/screenshots/ folder

Conclusion
Task 3 demonstrated live packet capture using Wireshark showing HTTP, TCP, ARP and DNS traffic. John the Ripper successfully identified and loaded an MD5 password hash for cracking. Hydra successfully brute-forced FTP credentials on Metasploitable, confirming the dangers of weak passwords and unencrypted protocols.
