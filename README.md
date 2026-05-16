This was created for those who operate in the light; 
because the adversaries are already capable, hiding in the shadows.

Part of a purple teaming compendium, red = offender, blue = defender, purple = forensic analysis, post IR remediation 

═══════════════════════════════════════════════════════════════════════════════

🔴 THE RED BOOK 10: Command & Control - FIELD OPERATOR'S FINAL COMPENDIUM
═══════════════════════════════════════════════════════════════════════════════

[License: Educational Use Only] [Status: Living Document] [Version: 1.0.0]


"The Operator's Bible for Authorized Red Team Engagements"
DNS, DoH, ICMP, and HTTP/3 C2 tunnels — minimal, stealthy, and OPSEC-driven.


═══════════════════════════════════════════════════════════════════════════════
📜 IMPORTANT — READ THIS FIRST
═══════════════════════════════════════════════════════════════════════════════

LEGAL WARNING — NOT OPTIONAL

This repository contains functional command and control (C2) tooling.
Unauthorized use is a serious crime.

By accessing, downloading, or using any code, documentation, or techniques in
this repository, you affirm that:

1. You have explicit, written permission from the owner of every system,
   network, and application you test.

2. Your authorized scope explicitly includes the techniques and tools you
   intend to deploy.

3. You are operating within all applicable laws in your jurisdiction
   (including but not limited to the Computer Fraud and Abuse Act (CFAA)
   in the US, the Computer Misuse Act in the UK, and similar legislation
   worldwide).

4. You understand that "authorized to test" does not mean "authorized to use
   any technique" — you must confirm that your scope includes DNS tunneling,
   ICMP exfiltration, or any other method before deployment.


EVEN WITH AUTHORIZATION, SCOPE MATTERS

✅ You have written permission to test api.target.com
❌ That does NOT authorize you to deploy DNS tunnels through target.com's DNS servers
❌ That does NOT authorize you to use ICMP tunneling through their network infrastructure
❌ That does NOT authorize you to persist through reboots without explicit permission

REAL-WORLD EXAMPLE:
A red teamer with full authorization to test a company's web application deployed
a DNS tunnel to exfiltrate data. The company's security team detected it, assumed
it was a real attacker, and the red teamer was terminated and faced legal action
because DNS tunneling was explicitly excluded from the scope agreement.

YOUR SCOPE AGREEMENT IS A LEGAL CONTRACT. TREAT IT AS SUCH.


LIABILITY

The authors and contributors assume NO LIABILITY for any misuse of this material.
This repository is provided for:

- Educational purposes in controlled environments
- Authorized red team engagements with explicit scope inclusion
- Security research in isolated labs

If you are unsure whether your use case is authorized, STOP and ask for written
clarification before proceeding.


═══════════════════════════════════════════════════════════════════════════════
📖 TABLE OF CONTENTS
═══════════════════════════════════════════════════════════════════════════════

1. Overview
2. Philosophy: OPSEC-Driven Development
3. C2 Tunnels Included
4. Quick Start
5. Size & Stealth Comparison
6. Build & Deployment
7. Legalities Deep Dive
8. License


═══════════════════════════════════════════════════════════════════════════════
OVERVIEW
═══════════════════════════════════════════════════════════════════════════════

The Red Book is a complete, self-contained HTML compendium of stealth C2
techniques. It provides:

- Working server and client implementations for 4 tunnel types
  (DNS, DoH, ICMP, HTTP/3)
- Each tunnel in both Python (rapid prototyping) and C (minimal binary)
- OPSEC-driven development principles — teaching why "bad code" is better
  for implants
- Copy-paste ready code blocks with one-click copying
- A quick reference cheat sheet for one-liners and persistence


WHY ANOTHER C2 FRAMEWORK?

Most C2 frameworks prioritize features and reliability over stealth.
The Red Book takes the opposite approach:

┌─────────────────────┬─────────────────────┬─────────────────────┐
│ Priority            │ Traditional C2      │ The Red Book        │
├─────────────────────┼─────────────────────┼─────────────────────┤
│ 1                   │ Reliability         │ Stealth / Size      │
│ 2                   │ Features            │ Reliability (just enough) │
│ 3                   │ UX                  │ Correctness         │
│ 4                   │ Logging             │ No logs             │
└─────────────────────┴─────────────────────┴─────────────────────┘

THE SMALLEST IMPLANT WINS. AV signatures, EDR hooks, and network detection
all scale with complexity.


═══════════════════════════════════════════════════════════════════════════════
PHILOSOPHY: OPSEC-DRIVEN DEVELOPMENT
═══════════════════════════════════════════════════════════════════════════════

The code in this repository deliberately violates software engineering best
practices. Here's why:

THE 7 COMMANDMENTS OF IMPLANT CODING

1. No error messages — ever. Fail silently or exit.
2. No logging — nothing written to disk unless required.
3. Jitter everything — sleeps, reconnects, beacon intervals.
4. Exponential backoff — don't hammer failed connections.
5. Masquerade or stay small — either blend in or be invisible.
6. No unnecessary dependencies — static link or pure system calls.
7. Strings kill — XOR or obfuscate any static string.

ANTI-PATTERNS THAT ARE ACTUALLY GOOD

┌─────────────────────────────┬─────────────────────────────────────────────┐
│ "Bad" Practice              │ Why It's Good for C2                        │
├─────────────────────────────┼─────────────────────────────────────────────┤
│ No error checking           │ Silent failures don't alert defenders      │
│ Hardcoded everything        │ No config file = no forensic artifact      │
│ Tiny variable names (s,c,b) │ No context for reverse engineers          │
│ No logging                  │ Nothing to find on disk                    │
│ Unexpected protocol behavior│ Signature detection expects standard       │
└─────────────────────────────┴─────────────────────────────────────────────┘

A software engineer will tell you to add error handling, retransmission,
sequence numbers, and logging. Do not do this. Each addition increases your
detection surface.


═══════════════════════════════════════════════════════════════════════════════
C2 TUNNELS INCLUDED
═══════════════════════════════════════════════════════════════════════════════

1. DNS TUNNEL
   How it works:   Commands encoded in DNS TXT queries
   Detection risk: Low (DNS is almost always allowed)
   Size (C client): ~1.5 KB (stripped, static)
   Use when:       You need to bypass firewalls, HTTP inspection

2. DNS OVER HTTPS (DoH) TUNNEL
   How it works:   DNS queries inside HTTPS (port 443)
   Detection risk: Very low (looks like normal web traffic)
   Size (C client): ~6 KB (with libcurl)
   Use when:       Corporate DNS is monitored or filtered

3. ICMP TUNNEL (Ping C2)
   How it works:   Data embedded in ICMP Echo Request packets
   Detection risk: Low (ICMP often allowed, but monitored)
   Size (C client): ~2 KB
   Note:           Requires raw sockets (root/admin)

4. HTTP/3 (QUIC) TUNNEL
   How it works:   UDP-based HTTP/3, bypasses TCP inspection
   Detection risk: Low (newer protocol, less inspected)
   Size (Python client): ~500 bytes (requires aioquic)
   Use when:       Advanced evasion needed


═══════════════════════════════════════════════════════════════════════════════
QUICK START
═══════════════════════════════════════════════════════════════════════════════

1. DOWNLOAD THE RED BOOK

   # Save the HTML file
   curl -O https://raw.githubusercontent.com/yourrepo/red_book/red_book.html

   # Or simply open in any browser
   firefox red_book.html


2. CHOOSE YOUR TUNNEL

   - Need stealth above all?        → ICMP or DoH
   - Need to bypass firewalls?      → DNS
   - Need modern evasion?           → HTTP/3


3. DEPLOY THE CLIENT

   # Linux target (minimal C client)
   gcc -Os -s -static -o beacon dns_beacon.c
   upx --best --ultra-brute beacon
   ./beacon

   # Windows target (cross-compile from Linux)
   x86_64-w64-mingw32-gcc -Os -s -static -o beacon.exe dns_beacon.c -lws2_32
   beacon.exe

   # Python target (any OS)
   python3 dns_beacon.py


4. START THE SERVER

   # Python DNS server (requires root for port 53)
   sudo python3 dns_server.py


5. SEND COMMANDS

   # On the server console
   > exec client-id whoami
   [+] Command queued for client-id: whoami
   > exec client-id id
   [+] Command queued for client-id: id


═══════════════════════════════════════════════════════════════════════════════
SIZE & STEALTH COMPARISON
═══════════════════════════════════════════════════════════════════════════════

┌─────────────────────────┬────────────┬──────────────────┬───────────────┬─────────────┐
│ Approach                │ Binary Size│ Dependencies     │ Detectability │ Reliability │
├─────────────────────────┼────────────┼──────────────────┼───────────────┼─────────────┤
│ Minimal C (no deps)     │ 1.5-3 KB   │ None             │ Very Low      │ Low         │
│ Hybrid C (basic recovery│ 6-12 KB    │ None             │ Low           │ Medium      │
│ Production C (full)     │ 50-200 KB  │ None             │ High          │ High        │
│ Python script           │ 500 B-2 KB │ Python interpreter│ Medium       │ Low         │
│ PowerShell one-liner    │ ~400 B     │ PowerShell       │ Medium        │ Low         │
└─────────────────────────┴────────────┴──────────────────┴───────────────┴─────────────┘

The Red Book provides Minimal and Hybrid C implementations. Production features
are intentionally omitted.


═══════════════════════════════════════════════════════════════════════════════
BUILD & DEPLOYMENT
═══════════════════════════════════════════════════════════════════════════════

LINUX (CLIENT & SERVER)

   # Install dependencies (Ubuntu/Debian)
   sudo apt update
   sudo apt install build-essential upx python3 python3-pip

   # Compile C client
   gcc -Os -ffunction-sections -fdata-sections -Wl,--gc-sections -s -static -o beacon dns_beacon.c

   # Compress (optional, reduces size 50-70%)
   upx --best --ultra-brute beacon

   # Run Python server
   sudo python3 dns_server.py


WINDOWS (CLIENT ONLY)

   # Cross-compile from Linux
   x86_64-w64-mingw32-gcc -Os -s -static -o beacon.exe dns_beacon.c -lws2_32

   # Or compile natively with MinGW
   gcc -Os -s -static -o beacon.exe dns_beacon.c -lws2_32


DOCKER (SERVER)

   FROM python:3.11-slim
   COPY dns_server.py /app/
   EXPOSE 53/udp
   CMD ["python3", "/app/dns_server.py"]

   docker build -t redbook-dns .
   docker run -p 53:53/udp redbook-dns


═══════════════════════════════════════════════════════════════════════════════
LEGALITIES DEEP DIVE
═══════════════════════════════════════════════════════════════════════════════

WHAT "EXPLICIT WRITTEN PERMISSION" ACTUALLY MEANS

A verbal "go ahead" or a Slack message does not constitute legal authorization.
You need:

1. A signed contract or statement of work that explicitly names the targets,
   duration, and allowed techniques.

2. A scope document that lists allowed testing methods (e.g., "DNS tunneling
   permitted," "ICMP exfiltration permitted").

3. Emergency contact information for the client's security team.

4. Rules of engagement that define what constitutes a violation.


SCOPE CREEP — THE MOST COMMON LEGAL TRAP

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

IF YOU FIND A VULNERABILITY that requires a technique outside your scope to
demonstrate, stop. Document what you found and ask for scope expansion before
proceeding.


REAL-WORLD CONSEQUENCES

┌─────────────────────────────────┬───────────────────────────────────────────┐
│ Violation                       │ Potential Consequence                    │
├─────────────────────────────────┼───────────────────────────────────────────┤
│ Testing out-of-scope IPs        │ Contract termination, blacklisting       │
│ Using unauthorized techniques   │ Lawsuit, criminal charges (CFAA)         │
│ Exfiltrating data               │ Criminal prosecution, prison time        │
│ Installing persistence          │ Charged with unauthorized access         │
│ Scanning third-party infra      │ Legal action from the third party        │
└─────────────────────────────────┴───────────────────────────────────────────┘


AUTHORIZATION CHECKLIST

Before running any code from this repository:

□ Do I have a signed, written agreement?
□ Does the scope explicitly include the techniques I plan to use?
□ Does the scope explicitly include the targets I plan to test?
□ Have I notified the client's security team of my testing window?
□ Do I have emergency contact information?
□ Am I operating within applicable laws?

If you answered "no" to any of these, stop and get clarification before
proceeding.


RECOMMENDED LEGAL REVIEW

Have a qualified attorney review:
- Your testing agreement
- The scope document
- Your proposed methodology

DO NOT RELY ON THIS README OR ANY ONLINE RESOURCE FOR LEGAL ADVICE.


═══════════════════════════════════════════════════════════════════════════════
LICENSE
═══════════════════════════════════════════════════════════════════════════════

EDUCATIONAL AND AUTHORIZED USE ONLY

You MAY:
   - Use this material for educational purposes in controlled environments
   - Use this material for authorized red team engagements with explicit
     scope inclusion
   - Modify and adapt the code for your own authorized use cases

You MAY NOT:
   - Use this material against any system without explicit written permission
   - Distribute modified versions as "production ready" without legal warnings
   - Remove or obscure these license terms and legal warnings


═══════════════════════════════════════════════════════════════════════════════
ACKNOWLEDGMENTS
═══════════════════════════════════════════════════════════════════════════════

- The red team community for sharing OPSEC tradecraft
- Offensive security researchers who prioritize stealth over features
- Defenders who make us better


═══════════════════════════════════════════════════════════════════════════════
CONTACT & REPORTING
═══════════════════════════════════════════════════════════════════════════════

If you find an issue with this repository: Open an issue on GitHub.

If you are being targeted by unauthorized use of this tooling: Contact your
local law enforcement and cybersecurity incident response team immediately.


═══════════════════════════════════════════════════════════════════════════════
FINAL WARNING — READ AGAIN
═══════════════════════════════════════════════════════════════════════════════

> This is not a "hacking tool." This is an educational resource for authorized
> professionals.
>
> Unauthorized use will result in:
>    - Termination of employment or contracts
>    - Permanent banning from bug bounty platforms
>    - Civil lawsuits
>    - Criminal prosecution
>    - Prison time
>
> Get written permission. Stay in scope. Know the law.


═══════════════════════════════════════════════════════════════════════════════
The Red Book — For those who operate in the light, with permission,
and with respect for the law.

Version 1.0.0 | Last updated: 2026
═══════════════════════════════════════════════════════════════════════════════
