# Network Discovery & Vulnerability Assessment with Nmap

A hands-on cybersecurity project documenting real-world network scanning, host enumeration, and vulnerability identification using Nmap. Built as part of my ongoing effort to develop practical, employer-ready skills in network security.

---

## Why This Project

Most of what gets taught about Nmap stops at `nmap -sV target`. That's a starting point, not a skill. This project goes further — it's a structured walkthrough of how I think through network reconnaissance: what I'm looking for, why I'm running a specific scan, what the output actually means, and how an attacker or defender would act on that information.

If you're a recruiter or hiring manager reading this: this isn't a tutorial copy-paste. Every scan in here was run against a lab environment I set up myself, and every finding is something I interpreted and documented from scratch.

---

## What's Covered

- **Host Discovery** — Identifying live hosts on a network without triggering noise. Techniques include ping sweeps, ARP scans, and no-ping scans for hosts that block ICMP.
- **Port Scanning** — TCP SYN scans, UDP scans, and full connect scans. Understanding why each one behaves differently and when to use them.
- **Service & Version Detection** — Pulling banner information and identifying what's actually running behind open ports, not just what *should* be running.
- **OS Fingerprinting** — Using Nmap's OS detection engine to identify operating systems based on TCP/IP stack behavior.
- **NSE (Nmap Scripting Engine)** — Writing and running scripts for tasks like SMB enumeration, vulnerability checks, brute-force detection, and HTTP header analysis.
- **Firewall & IDS Evasion** — Fragmented packets, decoy scans, timing adjustments — understanding how scans can be tuned to avoid detection or bypass basic filtering.
- **Output & Reporting** — Saving results in XML, grepable, and normal formats. Parsing output for use in downstream tools like Metasploit or custom scripts.

---

## Lab Environment

All scans were conducted in an isolated local lab. No unauthorized scanning was performed at any point. The environment consists of:

- **Attacker Machine:** Kali Linux (VM)
- **Target Machines:** Metasploitable 2, intentionally vulnerable Windows Server, custom Ubuntu host
- **Network:** Host-only/NAT network configured in VirtualBox to prevent any external exposure

If you want to replicate this, I'd strongly recommend using the same setup. Never run these scans on networks you don't own or have written permission to test.

---

## Project Structure

```
nmap-network-discovery/
│
├── scans/
│   ├── host-discovery/
│   ├── port-scans/
│   ├── service-version/
│   ├── os-detection/
│   ├── nse-scripts/
│   └── evasion-techniques/
│
├── reports/
│   ├── raw-output/         # .xml and .gnmap files from each scan
│   └── findings-summary/   # Interpreted findings written in plain English
│
├── scripts/
│   └── custom-nse/         # Any custom NSE scripts written during the project
│
└── notes/
    └── lessons-learned.md  # Running doc of things that didn't go as expected
```

---

## Sample Commands & What They're Actually Doing

**Basic host discovery on a subnet:**
```bash
nmap -sn 192.168.1.0/24
```
> Sends ARP requests (on local network) or ICMP echo requests to find which hosts are up. No port scanning — just mapping who's alive. Useful as a first pass before deciding where to dig deeper.

---

**SYN scan with version detection and default scripts:**
```bash
nmap -sS -sV -sC -p- 192.168.1.105
```
> `-sS` does a half-open scan (never completes the TCP handshake, slightly stealthier). `-sV` pulls service versions. `-sC` runs default NSE scripts. `-p-` scans all 65535 ports instead of just the top 1000. This is my go-to for a thorough single-host assessment.

---

**OS detection with aggressive timing:**
```bash
nmap -O -T4 192.168.1.105
```
> Tries to identify the OS based on how the target's TCP/IP stack responds to crafted packets. `-T4` speeds up the scan — useful in lab environments, but would be noisy in a real engagement.

---

**Running a vulnerability script against SMB:**
```bash
nmap --script smb-vuln-ms17-010 -p 445 192.168.1.105
```
> Checks for EternalBlue (MS17-010), the vulnerability behind WannaCry. If this comes back positive on a machine, it's unpatched and potentially exploitable via Metasploit's `exploit/windows/smb/ms17_010_eternalblue`.

---

**Decoy scan for evasion:**
```bash
nmap -D RND:10 192.168.1.105
```
> Generates 10 random decoy IP addresses alongside the real scan source. Makes it harder for an IDS/IPS to identify the actual origin of the scan. Doesn't make you invisible — just adds noise.

---

## Key Findings from Lab Scans

| Host | Open Ports | Notable Services | Risk Level |
|---|---|---|---|
| Metasploitable 2 | 21, 22, 23, 25, 80, 3306... | vsftpd 2.3.4 (backdoor), Telnet, MySQL | Critical |
| Win Server (unpatched) | 445, 3389 | SMBv1 enabled, RDP exposed | High |
| Ubuntu host | 22, 80 | OpenSSH 7.2, Apache 2.4.18 | Medium |

Each finding has a corresponding write-up in `/reports/findings-summary/` explaining what the service is, what the associated risk is, and what remediation would look like.

---

## What I Learned

A few things that actually surprised me while working through this:

- **UDP scanning is painful.** It's slow, unreliable, and easy to misread. Open UDP ports often look identical to filtered ones unless you get a specific response. I spent more time on UDP than I expected.
- **NSE scripts vary wildly in quality.** Some are rock solid, others produce false positives. Always verify script output manually before including it in a report.
- **Timing matters more than I thought.** Aggressive timing profiles (`-T5`) caused missed ports on some targets because responses came back after the scan had moved on. `-T3` or `-T4` is usually the sweet spot.
- **Output formats are underrated.** The XML output from Nmap is incredibly useful for piping into other tools. I wrote a small Python script to parse it and extract just the data I needed.

---

## Tools Used Alongside Nmap

- **Wireshark** — Capturing traffic during scans to see exactly what packets are being sent and how targets respond
- **Metasploit** — Validating vulnerabilities flagged by NSE scripts
- **Python** — Parsing Nmap XML output
- **grep / awk** — Working with grepable output for quick filtering

---

## Skills Demonstrated

- Network reconnaissance methodology
- TCP/IP fundamentals (why scans work the way they do, not just that they do)
- Vulnerability identification and risk classification
- Documentation and reporting
- Responsible disclosure practices and lab ethics

---

## Disclaimer

Everything in this project was performed in a private, isolated lab environment. Unauthorized network scanning is illegal. If you're building something similar, get proper authorization in writing before running any scans outside your own infrastructure. Use platforms like [Hack The Box](https://www.hackthebox.com), [TryHackMe](https://tryhackme.com), or set up your own local lab.

---

## About Me

I'm a cybersecurity analyst focused on network security, vulnerability assessment, and threat detection. This project is one part of a broader portfolio I'm building to demonstrate practical, hands-on skills — not just theoretical knowledge.

Feel free to reach out if you want to talk through anything here or have questions about the methodology.
