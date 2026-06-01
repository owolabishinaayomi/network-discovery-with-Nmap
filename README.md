# Network Discovery with Nmap

A hands-on project documenting my practical use of Nmap for network scanning, host enumeration, service detection, and vulnerability identification. This is part of my cybersecurity portfolio, built to demonstrate real working knowledge of network reconnaissance and how it fits into a broader security assessment workflow.

---

## What This Project Covers

- **Host Discovery** — Identifying live hosts on a network using ping sweeps, ARP scans, and no-ping techniques for hosts that block ICMP.
- **Port Scanning** — Running TCP SYN, UDP, and full connect scans to map open ports and understand what's exposed.
- **Service & Version Detection** — Pulling banner information to identify exactly what's running behind open ports.
- **OS Fingerprinting** — Using Nmap's detection engine to identify operating systems based on TCP/IP stack behavior.
- **NSE (Nmap Scripting Engine)** — Using built-in scripts for SMB enumeration, vulnerability checks, and HTTP analysis.
- **Evasion Techniques** — Exploring how scan timing, fragmentation, and decoys affect detectability.
- **Output & Reporting** — Saving and interpreting results in XML, grepable, and normal formats.

---

## Tools & Environment

- **Nmap** — Primary tool for all scanning and enumeration
- **Kali Linux** — Attacker machine running in a local VM
- **Metasploitable 2 + intentionally vulnerable hosts** — All scans were performed in an isolated lab environment

> No unauthorized scanning was performed at any point. Everything here was done in a private lab I set up myself.

---

## Sample Commands

**Discover live hosts on a subnet:**
```bash
nmap -sn 192.168.1.0/24
```

**Full port scan with version detection and default scripts:**
```bash
nmap -sS -sV -sC -p- 192.168.1.105
```

**OS detection:**
```bash
nmap -O -T4 192.168.1.105
```

**Check for EternalBlue (MS17-010) via SMB:**
```bash
nmap --script smb-vuln-ms17-010 -p 445 192.168.1.105
```

**Decoy scan to obscure scan origin:**
```bash
nmap -D RND:10 192.168.1.105
```

---

## Skills Demonstrated

- Network reconnaissance methodology
- TCP/IP fundamentals and how scanning techniques actually work under the hood
- Vulnerability identification and risk classification
- Security documentation and reporting
- Responsible lab practices

---

## Disclaimer

All scanning activity in this project was conducted in a private, isolated lab environment. Scanning networks without authorization is illegal. Always get written permission before testing any infrastructure you don't personally own.
