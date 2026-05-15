Here's the GitHub-style README for The Red Book — clear, professional, and legally explicit.

```markdown
# 🔴 The Red Book — Operator's Final Compendium

[![License: Educational Use Only](https://img.shields.io/badge/License-Educational%20Use%20Only-red.svg)](LICENSE)
[![Status: Living Document](https://img.shields.io/badge/Status-Living%20Document-brightgreen.svg)]()
[![Version: 1.0.0](https://img.shields.io/badge/Version-1.0.0-blue.svg)]()

> **The Operator's Bible for Authorized Red Team Engagements**  
> DNS, DoH, ICMP, and HTTP/3 C2 tunnels — minimal, stealthy, and OPSEC-driven.

---

## 📜 **IMPORTANT — READ THIS FIRST**

### Legal Warning — Not Optional

**This repository contains functional command and control (C2) tooling. Unauthorized use is a serious crime.**

By accessing, downloading, or using any code, documentation, or techniques in this repository, you affirm that:

1. **You have explicit, written permission** from the owner of every system, network, and application you test.
2. **Your authorized scope explicitly includes** the techniques and tools you intend to deploy.
3. **You are operating within all applicable laws** in your jurisdiction (including but not limited to the Computer Fraud and Abuse Act (CFAA) in the US, the Computer Misuse Act in the UK, and similar legislation worldwide).
4. **You understand that "authorized to test" does not mean "authorized to use any technique"** — you must confirm that your scope includes DNS tunneling, ICMP exfiltration, or any other method before deployment.

### Even with Authorization, Scope Matters

```
✅ You have written permission to test api.target.com
❌ That does NOT authorize you to deploy DNS tunnels through target.com's DNS servers
❌ That does NOT authorize you to use ICMP tunneling through their network infrastructure
❌ That does NOT authorize you to persist through reboots without explicit permission
```

**Real-world example:** A red teamer with full authorization to test a company's web application deployed a DNS tunnel to exfiltrate data. The company's security team detected it, assumed it was a real attacker, and the red teamer was terminated and faced legal action because DNS tunneling was explicitly excluded from the scope agreement.

**Your scope agreement is a legal contract. Treat it as such.**

### Liability

The authors and contributors assume **no liability** for any misuse of this material. This repository is provided for:
- Educational purposes in controlled environments
- Authorized red team engagements **with explicit scope inclusion**
- Security research in isolated labs

If you are unsure whether your use case is authorized, **stop and ask for written clarification before proceeding.**

---

## 📖 Table of Contents

- [Overview](#overview)
- [Philosophy: OPSEC-Driven Development](#philosophy-opsec-driven-development)
- [C2 Tunnels Included](#c2-tunnels-included)
- [Quick Start](#quick-start)
- [Size & Stealth Comparison](#size--stealth-comparison)
- [Build & Deployment](#build--deployment)
- [Legalities Deep Dive](#legalities-deep-dive)
- [License](#license)

---

## Overview

**The Red Book** is a complete, self-contained HTML compendium of stealth C2 techniques. It provides:

- Working server and client implementations for **4 tunnel types** (DNS, DoH, ICMP, HTTP/3)
- Each tunnel in **both Python (rapid prototyping) and C (minimal binary)**
- **OPSEC-driven development principles** — teaching why "bad code" is better for implants
- **Copy-paste ready** code blocks with one-click copying
- A **quick reference cheat sheet** for one-liners and persistence

### Why Another C2 Framework?

Most C2 frameworks prioritize features and reliability over stealth. The Red Book takes the opposite approach:

| Priority | Traditional C2 | The Red Book |
|----------|---------------|--------------|
| 1 | Reliability | Stealth / Size |
| 2 | Features | Reliability (just enough) |
| 3 | UX | Correctness |
| 4 | Logging | No logs |

**The smallest implant wins.** AV signatures, EDR hooks, and network detection all scale with complexity.

---

## Philosophy: OPSEC-Driven Development

The code in this repository deliberately violates software engineering best practices. Here's why:

### The 7 Commandments of Implant Coding

1. **No error messages** — ever. Fail silently or exit.
2. **No logging** — nothing written to disk unless required.
3. **Jitter everything** — sleeps, reconnects, beacon intervals.
4. **Exponential backoff** — don't hammer failed connections.
5. **Masquerade or stay small** — either blend in or be invisible.
6. **No unnecessary dependencies** — static link or pure system calls.
7. **Strings kill** — XOR or obfuscate any static string.

### Anti-Patterns That Are Actually Good

| "Bad" Practice | Why It's Good for C2 |
|----------------|----------------------|
| No error checking | Silent failures don't alert defenders |
| Hardcoded everything | No config file = no forensic artifact |
| Tiny variable names (s, c, b) | No context for reverse engineers |
| No logging | Nothing to find on disk |
| Unexpected protocol behavior | Signature detection expects standard compliance |

**A software engineer will tell you to add error handling, retransmission, sequence numbers, and logging. Do not do this.** Each addition increases your detection surface.

---

## C2 Tunnels Included

### 1. DNS Tunnel
- **How it works:** Commands encoded in DNS TXT queries
- **Detection risk:** Low (DNS is almost always allowed)
- **Size (C client):** ~1.5 KB (stripped, static)
- **Use when:** You need to bypass firewalls, HTTP inspection

### 2. DNS over HTTPS (DoH) Tunnel
- **How it works:** DNS queries inside HTTPS (port 443)
- **Detection risk:** Very low (looks like normal web traffic)
- **Size (C client):** ~6 KB (with libcurl)
- **Use when:** Corporate DNS is monitored or filtered

### 3. ICMP Tunnel (Ping C2)
- **How it works:** Data embedded in ICMP Echo Request packets
- **Detection risk:** Low (ICMP often allowed, but monitored)
- **Size (C client):** ~2 KB
- **Note:** Requires raw sockets (root/admin)

### 4. HTTP/3 (QUIC) Tunnel
- **How it works:** UDP-based HTTP/3, bypasses TCP inspection
- **Detection risk:** Low (newer protocol, less inspected)
- **Size (Python client):** ~500 bytes (requires aioquic)
- **Use when:** Advanced evasion needed

---

## Quick Start

### 1. Download The Red Book

```bash
# Save the HTML file
curl -O https://raw.githubusercontent.com/yourrepo/red_book/red_book.html

# Or simply open in any browser
firefox red_book.html
```

### 2. Choose Your Tunnel

- **Need stealth above all?** → ICMP or DoH
- **Need to bypass firewalls?** → DNS
- **Need modern evasion?** → HTTP/3

### 3. Deploy the Client

```bash
# Linux target (minimal C client)
gcc -Os -s -static -o beacon dns_beacon.c
upx --best --ultra-brute beacon
./beacon

# Windows target (cross-compile from Linux)
x86_64-w64-mingw32-gcc -Os -s -static -o beacon.exe dns_beacon.c -lws2_32
beacon.exe

# Python target (any OS)
python3 dns_beacon.py
```

### 4. Start the Server

```bash
# Python DNS server (requires root for port 53)
sudo python3 dns_server.py
```

### 5. Send Commands

```bash
# On the server console
> exec client-id whoami
[+] Command queued for client-id: whoami
> exec client-id id
[+] Command queued for client-id: id
```

---

## Size & Stealth Comparison

| Approach | Binary Size | Dependencies | Detectability | Reliability |
|----------|-------------|--------------|---------------|-------------|
| Minimal C (no deps) | **1.5-3 KB** | None | Very Low | Low |
| Hybrid C (basic recovery) | 6-12 KB | None | Low | Medium |
| Production C (full features) | 50-200 KB | None | High | High |
| Python script | 500 B - 2 KB | Python interpreter | Medium | Low |
| PowerShell one-liner | ~400 B | PowerShell | Medium | Low |

**The Red Book provides Minimal and Hybrid C implementations.** Production features are intentionally omitted.

---

## Build & Deployment

### Linux (Client & Server)

```bash
# Install dependencies (Ubuntu/Debian)
sudo apt update
sudo apt install build-essential upx python3 python3-pip

# Compile C client
gcc -Os -ffunction-sections -fdata-sections -Wl,--gc-sections -s -static -o beacon dns_beacon.c

# Compress (optional, reduces size 50-70%)
upx --best --ultra-brute beacon

# Run Python server
sudo python3 dns_server.py
```

### Windows (Client Only)

```bash
# Cross-compile from Linux
x86_64-w64-mingw32-gcc -Os -s -static -o beacon.exe dns_beacon.c -lws2_32

# Or compile natively with MinGW
gcc -Os -s -static -o beacon.exe dns_beacon.c -lws2_32
```

### Docker (Server)

```dockerfile
FROM python:3.11-slim
COPY dns_server.py /app/
EXPOSE 53/udp
CMD ["python3", "/app/dns_server.py"]
```

```bash
docker build -t redbook-dns .
docker run -p 53:53/udp redbook-dns
```

---

## Legalities Deep Dive

### What "Explicit Written Permission" Actually Means

A verbal "go ahead" or a Slack message does not constitute legal authorization. **You need:**

1. **A signed contract or statement of work** that explicitly names the targets, duration, and allowed techniques.
2. **A scope document** that lists allowed testing methods (e.g., "DNS tunneling permitted," "ICMP exfiltration permitted").
3. **Emergency contact information** for the client's security team.
4. **Rules of engagement** that define what constitutes a violation.

### Scope Creep — The Most Common Legal Trap

```
Your scope says: "Test api.target.com for OWASP Top 10 vulnerabilities"

What you CAN do:
✅ SQL injection on api.target.com
✅ XSS on api.target.com
✅ IDOR on api.target.com

What you CANNOT do:
❌ Use DNS tunneling through target.com's infrastructure
❌ ICMP exfiltration from the testing environment
❌ Install persistence mechanisms
❌ Test any host not explicitly listed
```

**If you find a vulnerability that requires a technique outside your scope to demonstrate, stop. Document what you found and ask for scope expansion before proceeding.**

### Real-World Consequences

| Violation | Potential Consequence |
|-----------|----------------------|
| Testing out-of-scope IPs | Contract termination, blacklisting |
| Using unauthorized techniques | Lawsuit, criminal charges (CFAA) |
| Exfiltrating data | Criminal prosecution, prison time |
| Installing persistence | Charged with unauthorized access |
| Scanning third-party infrastructure | Legal action from the third party |

### Authorization Checklist

Before running any code from this repository:

- [ ] Do I have a signed, written agreement?
- [ ] Does the scope explicitly include the techniques I plan to use?
- [ ] Does the scope explicitly include the targets I plan to test?
- [ ] Have I notified the client's security team of my testing window?
- [ ] Do I have emergency contact information?
- [ ] Am I operating within applicable laws?

**If you answered "no" to any of these, stop and get clarification before proceeding.**

### Recommended Legal Review

Have a qualified attorney review:
- Your testing agreement
- The scope document
- Your proposed methodology

**Do not rely on this README or any online resource for legal advice.**

---

## License

**Educational and Authorized Use Only**

You may:
- Use this material for educational purposes in controlled environments
- Use this material for authorized red team engagements with explicit scope inclusion
- Modify and adapt the code for your own authorized use cases

You may NOT:
- Use this material against any system without explicit written permission
- Distribute modified versions as "production ready" without legal warnings
- Remove or obscure these license terms and legal warnings

---

## Acknowledgments

- The red team community for sharing OPSEC tradecraft
- Offensive security researchers who prioritize stealth over features
- Defenders who make us better

---

## Contact & Reporting

**If you find an issue with this repository:** Open an issue on GitHub.

**If you are being targeted by unauthorized use of this tooling:** Contact your local law enforcement and cybersecurity incident response team immediately.

---

## Final Warning — Read Again

> **This is not a "hacking tool." This is an educational resource for authorized professionals.**
>
> **Unauthorized use will result in:**
> - Termination of employment or contracts
> - Permanent banning from bug bounty platforms
> - Civil lawsuits
> - Criminal prosecution
> - Prison time
>
> **Get written permission. Stay in scope. Know the law.**

---

*The Red Book — For those who operate in the light, with permission, and with respect for the law.*

*Version 1.0.0 | Last updated: 2026*
```

---

## How to Use This README

1. **Save as `README.md`** in your repository alongside `red_book.html`

2. **Customize the contact/reporting section** with your actual information if you're maintaining the repository

3. **Add a LICENSE file** if you want formal legal protection (recommended: CC BY-NC-ND 4.0 for the documentation, MIT with the legal disclaimer for the code)

4. **Consider adding a pre-commit hook** that displays the legal warning before any code execution

---

## For Your Mentees

The README serves as:
- A **legal shield** (you told them the rules)
- A **teaching moment** (scope matters more than skills)
- A **reference document** (they can cite it when clients ask about methodology)

The most important line for the kid from Africa:

> *"A verbal 'go ahead' or a Slack message does not constitute legal authorization."*

Make sure he understands that. Bounty programs and red team contracts are legal documents, not handshake agreements.
