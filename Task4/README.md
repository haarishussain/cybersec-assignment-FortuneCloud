Task 4 — Malware, System Security & Cloud Security

What This Task Is About
This task covers the defensive side of cybersecurity — understanding different malware types, hardening a Linux system, and learning about tools like firewalls, IDS/IPS, and SIEM systems. I also compared offensive and defensive security approaches.

Environment
- Kali Linux running on VMware
- Metasploitable 2 (192.168.118.129) used as reference

---

Part 1 — Malware Types

During this module I studied the most common types of malware and how they work:

**Virus** — attaches itself to a file and spreads when that file is executed. Needs human action to spread.

**Worm** — spreads on its own through networks without any user interaction. WannaCry is a good example.

**Trojan** — looks like a normal program but does something malicious in the background. RATs (Remote Access Trojans) fall into this category.

**Ransomware** — encrypts all your files and asks for payment to get them back. Very common in recent years (LockBit, REvil).

**Spyware** — silently collects your information without you knowing.

**Rootkit** — hides deep inside the OS so antivirus tools can't easily detect it.

**Keylogger** — records everything you type, mainly to steal passwords.

**Backdoor** — creates a hidden entry point into a system. We actually saw this on Metasploitable — port 1524 is an open root shell backdoor.

**Botnet** — a bunch of infected machines controlled by one attacker. Used for DDoS attacks mostly.

How Malware is Analyzed
- **Static analysis** — look at the file without running it (check strings, file type, hash)
- **Dynamic analysis** — run it in a safe sandbox and see what it does (network connections, file changes)
- Tools used in real world: VirusTotal, Cuckoo Sandbox, Ghidra

---

Part 2 — Linux System Hardening

I ran the following commands on Kali Linux to check the system's security posture:

```bash
# See what services are currently running
sudo systemctl list-units --type=service --state=running

# Check which ports are open and what's listening
sudo ss -tulnp

# View current firewall rules
sudo iptables -L

# Check sudo permissions
sudo cat /etc/sudoers | grep -v "#" | grep -v "^$"
```

What I Found
- iptables had no rules set (all chains on ACCEPT) — meaning no firewall protection was active
- Only essential services were running on Kali
- sudo access was properly restricted to root and sudo group only

Good Hardening Practices
- Disable services you don't need
- Always keep the system updated
- Set up firewall rules — don't leave everything on ACCEPT
- Disable root login over SSH
- Use SSH keys instead of passwords
- Check logs regularly for anything suspicious

Windows Hardening (Research)
- Disable SMBv1 — it's what WannaCry exploited
- Enable BitLocker for encryption
- Keep Windows Update on
- Use Group Policy to limit what users can do
- Enable Windows Defender and configure it properly

---

Part 3 — Firewall, IDS/IPS, SIEM & Cloud Security

Firewall
Controls what traffic is allowed in and out of a network. Think of it as a security guard at the door.
- iptables and ufw are common on Linux
- Enterprise level: Palo Alto, Cisco ASA, pfSense

IDS vs IPS
- **IDS** just watches and alerts you when something suspicious happens — it doesn't stop it
- **IPS** actually blocks the threat in real time
- Tools: Snort, Suricata, OSSEC, Fail2Ban

SIEM
Collects logs from every system in your network and correlates them to find patterns that indicate an attack. Very important in enterprise environments.
- Popular tools: Splunk, IBM QRadar, Microsoft Sentinel
- Flow: collect logs → correlate events → trigger alerts → respond

Cloud Security
Cloud security is a shared responsibility — the provider (AWS/Azure/GCP) secures the physical infrastructure, but you are responsible for your data, configurations and access controls.
- Common mistakes: public S3 buckets, exposed API keys, no MFA on cloud accounts
- Key controls: IAM roles, encryption, VPC security groups, CloudTrail for audit logs

---

Part 4 — Offensive vs Defensive Security

| | Offensive (Red Team) | Defensive (Blue Team) |
|---|---|---|
| Goal | Find vulnerabilities before attackers do | Detect and stop attacks |
| Mindset | Think like an attacker | Think like a defender |
| Tools | Nmap, Metasploit, Hydra, Nikto | Snort, Splunk, iptables, Wireshark |
| Output | Pentest report with findings | Hardened systems, alert rules, patches |
| Timing | Scheduled tests | 24/7 continuous monitoring |

In practice both teams work together — this is called a **Purple Team** approach. Red team finds the gap, blue team fixes it immediately.

---
Screenshots
See /Task4/ folder

Summary
This task gave me a solid understanding of the defensive side of security. Setting up and checking firewall rules, understanding how malware works, and learning about enterprise tools like SIEM helped connect the dots between what attackers do (Tasks 1-3) and how defenders respond. The offensive vs defensive comparison made it clear that both sides are equally important in a real security setup.
