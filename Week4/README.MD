
```
███╗   ███╗███████╗██████╗ ██╗██████╗  ██████╗ ███████╗ █████╗
████╗ ████║██╔════╝██╔══██╗██║██╔══██╗██╔═══██╗╚══███╔╝██╔══██╗
██╔████╔██║█████╗  ██║  ██║██║██████╔╝██║   ██║  ███╔╝ ███████║
██║╚██╔╝██║██╔══╝  ██║  ██║██║██╔══██╗██║   ██║ ███╔╝  ██╔══██║
██║ ╚═╝ ██║███████╗██████╔╝██║██║  ██║╚██████╔╝███████╗██║  ██║
╚═╝     ╚═╝╚══════╝╚═════╝ ╚═╝╚═╝  ╚═╝ ╚═════╝ ╚══════╝╚═╝  ╚═╝

           PENETRATION TESTING ENGAGEMENT — WEEK 4
```

<div align="center">

### Black-Box Penetration Test — Mediroza General Hospital

![Status](https://img.shields.io/badge/status-in--progress-yellow?style=for-the-badge)
![Type](https://img.shields.io/badge/type-black--box%20pentest-blueviolet?style=for-the-badge)
![Duration](https://img.shields.io/badge/duration-5%20days-informational?style=for-the-badge)
![Authorization](https://img.shields.io/badge/authorization-GRANTED-success?style=for-the-badge)
![Program](https://img.shields.io/badge/program-Networkwalks-0a0a0a?style=for-the-badge)

`target: https://medirozahospital.com`

</div>

---

## `$ cat engagement_brief.txt`

| Field | Detail |
|---|---|
| **Client** | Mediroza General Hospital |
| **Engagement Type** | Full black-box penetration test & vulnerability assessment |
| **Target** | `https://medirozahospital.com` |
| **Duration** | 5 days, independent work |
| **Rules of Engagement** | Target domain only · no social engineering · no DoS · no out-of-scope testing |
| **Authorization** | Written client authorization on file |

> This engagement was conducted in a controlled, authorized educational lab environment as part of the Networkwalks training program. Techniques documented here were never applied to any system without explicit written permission from its owner.

---

## `$ ls milestones/`

```
[M1] Initial Access        — retrieve 3 confidential patient PDF lab reports
[M2] Data Extraction        — crack encryption on all 3 retrieved files
[M3] Critical Exposure       — uncover staff salaries + shareholder details
[M4] Pentest Report         — professional deliverable for client
```

### `▸ M1 — Initial Access` — `[STATUS: IN PROGRESS]`

**Objective:** Attack the website and retrieve 3 confidential patient PDF lab reports.

- [x] Reconnaissance on target (whatweb, whois, dig/nslookup)
- [ ] Identify exposed entry points
- [ ] Analyse authentication mechanism behaviour
- [ ] Probe input handling for weaknesses
- [ ] Gain unauthorised access to restricted area

**Recon so far:**
```
CMS           Mediroza CMS 1.4.2
Server        LiteSpeed
Meta author   Mediroza IT Department
Contact       info@medirozahospital.com
IP            199.188.201.16
WHOIS         Privacy-protected (Namecheap / WithheldForPrivacy)
DNS           Single A record, no interesting subdomains found yet
```

**Deliverable:** Proof of access + 3 retrieved PDF files — *pending*

---

### `▸ M2 — Data Extraction` — `[STATUS: NOT STARTED]`

**Objective:** Crack the encryption on all 3 retrieved files.

- [ ] Analyse encryption on each file
- [ ] Select tools/wordlists per file (no single approach assumed)
- [ ] Iterate when a method fails

**Deliverable:** Recovered contents of all 3 files with proof of access — *pending*

---

### `▸ M3 — Critical Exposure` — `[STATUS: NOT STARTED]`

**Objective:** Find the critical data exposure on the client server.

**Tasks:**
- [ ] Find salaries of all hospital employees
- [ ] Find shareholder details of the hospital

**Deliverable:** Documented evidence of exposure + readable summary — *pending*

---

### `▸ M4 — Pentest Report` — `[STATUS: NOT STARTED]`

**Objective:** Write a professional penetration testing report for the client.

**Structure:**
1. Executive Summary
2. Scope & Methodology
3. Findings & Proof of Exploitation
4. Risk Rating (Critical / High / Medium / Low)
5. Recommendations & Remediation

**Deliverable:** Complete professional pentest report — *pending*

---

## `$ tree .`

```
mediroza-penetration-testing/
├── M1-initial-access/
├── M2-data-extraction/
├── M3-critical-exposure/
├── M4-pentest-report/
└── README.md
```

---

## `$ whoami`

**Kitsana Thuekoh** — Penetration Tester & Offensive Security Researcher
OSCP · HTB CPTS · CompTIA PenTest+ · CompTIA Security+ · ISC2 CC

---

<div align="center">

*Part of the Networkwalks penetration testing training program.*
*This repository documents an authorized lab engagement for educational purposes only.*

</div>
