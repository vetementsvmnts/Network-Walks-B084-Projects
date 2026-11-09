<div align="center">

# Penetration Testing Report 

### Footprinting & Network Scanning Phases

**W2-PM-FINAL · Cybersecurity · Networkwalks**

![Phase](https://img.shields.io/badge/Phase-1%20Recon%20%26%20Footprinting-blue)
![Phase](https://img.shields.io/badge/Phase-2%20Scanning%20%26%20Discovery-blue)
![Status](https://img.shields.io/badge/Phases%203--5-In%20Progress-lightgrey)
![Authorized](https://img.shields.io/badge/Permission-Secured-brightgreen)

</div>

---

## Report Details

| Field | Detail |
|---|---|
| **Pentester** | Emmanuel Bafi |
| **Program / Batch** | B082-Networkwalks |
| **Date** | 17 August 2026 |
| **Modules completed** | W2-PM1 (Multiple Kali Tools) · W2-PM5 (Zenmap Scanning) |
| **Targets** | 1. Networkwalks (written permission secured)<br>2. My own local LAN |
| **Permission secured?** | ✅ Yes |
| **Phases covered** | **Phase 1:** Reconnaissance & Footprinting<br>**Phase 2:** Scanning & Network Discovery<br>**Phases 3–5:** In progress |

##  Table of Contents

1. [Liability Disclaimer](#1-liability-disclaimer)
2. [Introduction](#2-introduction)
3. [Tools Used](#3-tools-used)
4. [Activities Performed](#4-activities-performed)
   - [4.1 Footprinting & Reconnaissance](#41-footprinting--reconnaissance)
   - [4.2 Network Scanning with Zenmap](#42-network-scanning-with-zenmap)
5. [Risk Analysis / Impact](#5-risk-analysis--impact)
6. [Recommendations](#6-recommendations)
7. [Conclusion](#7-conclusion)

---

## 1. Liability Disclaimer

>  I performed these activities only on systems and devices where I had written permission, or on devices I own. All material here is for **education and research purposes only**. Do not use anything here to break the law.
>
> The instructor, the authors and Networkwalks are not responsible for what you do with this knowledge. Every action you take is your own responsibility. Misuse can lead to criminal charges, heavy fines, loss of employment and a permanent record. In most countries, unauthorised access is a crime even when nothing is damaged.

## 2. Introduction

This report covers footprinting the `networkwalks.com` domain with multiple Kali Linux tools (**W2-PM1**) and scanning my own local network with Zenmap (**W2-PM5**). One module covers footprinting and the other covers scanning, so together they show how an attacker moves from gathering public information to mapping live hosts on a network. This is the Week 2 deliverable of my internship program at Networkwalks.

Footprinting was done in Kali Linux; scanning was done on a Windows PC with Zenmap installed. Every step includes the command used, what I observed, a screenshot as evidence, and a short note on why the finding matters from an attacker's point of view.

## 3. Tools Used

| Tool | Purpose |
|---|---|
| **Kali Linux & Windows** | Operating systems used for reconnaissance |
| **WHOIS** | Domain registration details (owner, dates, name servers) |
| **whatweb** | Fingerprint web technologies (server, CMS, plugins, IP) |
| **nslookup** | Resolve the domain name to its IP address via DNS |
| **curl -I** | Read the HTTP response headers of the website |
| **wafw00f** | Detect whether a Web Application Firewall protects the site |
| **dnsrecon** | Enumerate DNS records (NS, MX, SPF, TXT, SRV) |
| **Zenmap (Nmap GUI)** | Scan the local subnet for live hosts, IPs and MAC addresses |
| **Windows CMD** | Identify local IP and MAC address |

## 4. Activities Performed

### 4.1 Footprinting & Reconnaissance

I ran reconnaissance against `networkwalks.com` using six Kali Linux tools. Each one gathered a different type of information.

####  WHOIS: domain registration

```bash
whois networkwalks.com
```

**Observation:** Returned public domain registration details and identified the domain's name servers, giving insight into the registration and hosting infrastructure.

![WHOIS output](Assets/whois.png)

<details>
<summary>Additional WHOIS output</summary>

![WHOIS output (continued)](Assets/whois2.png)

</details>

####  WhatWeb: technology fingerprinting

```bash
whatweb networkwalks.com
```

**Observation:** Identified **WordPress 7.0.4** and **WP Download Manager 3.3.58**, along with other information exposed by the site.

**Why it matters:** Version information lets an attacker look up known issues for the exact software in use.

![WhatWeb output](Assets/whatweb.png)

####  Nslookup: DNS resolution

```bash
nslookup networkwalks.com
```

**Observation:** The domain resolved to **`192.232.216.135`**.

**Why it matters:** Reveals the network location of the web service, which is the starting point for later scanning.

![Nslookup output](Assets/nslookup.png)

####  Curl: HTTP headers

```bash
curl -I https://networkwalks.com
```

**Observation:** The response headers gave extra detail about the web application and exposed the WordPress REST API endpoint **`/wp-json/`**.

**Why it matters:** Headers and exposed endpoints help with fingerprinting and further enumeration.

![Curl output](Assets/curl.png)

####  Wafw00f: WAF detection

```bash
wafw00f https://networkwalks.com
```

**Observation:** Detected **ModSecurity (SpiderLabs)** protecting the site.

**Why it matters:** Knowing which WAF is in place tells an attacker which filtering behaviour to expect.

![Wafw00f output](Assets/wafw00f.png)

####  DNSRecon: DNS enumeration

```bash
dnsrecon -d networkwalks.com
```

**Observation:** Enumerated name servers, mail servers, SPF/TXT records, service records and DNS software information.

**Why it matters:** DNS records help build a broader profile of the organisation's infrastructure.

![DNSRecon output](Assets/dnsrecon.png)

---

### 4.2 Network Scanning with Zenmap

For the second activity I used **Zenmap** to discover hosts on my local network. The practical required me to:

1. Identify my local IP address and subnet
2. Discover live hosts
3. Record their IP and MAC addresses
4. Generate a network topology

I used the Windows `ipconfig` command to find my IP and subnet, entered the subnet into Zenmap, and ran a **Ping Scan** to identify active hosts.

The example results from the practical showed four live hosts:

| # | Host |
|---|---|
| 1 | `10.0.0.1` |
| 2 | `10.0.0.4` |
| 3 | `10.0.0.19` |
| 4 | `10.0.0.5` |

The example results also included four MAC addresses. After the scan, I opened the **Topology** tab in Zenmap, enabled the legend, and saved the topology as a PDF as the practical required.

> **Note:** The subnet, host count and addresses above are the practical's example values and should be replaced with the results from my own network before submission.

<!-- TODO: add the Zenmap screenshots once they are uploaded, e.g.
![Zenmap ping scan](../W2-PM5/Assets/zenmap.png)
![Zenmap topology](../W2-PM5/Assets/zenmap2.png)
-->

---

## 5. Risk Analysis / Impact

Risk level key:  Critical ·  Medium ·  Low

| # | Risk / Finding | Evidence | Potential Impact | Level |
|---|---|---|---|---|
| 1 | Web technology information exposed | WhatWeb identified WordPress and WP Download Manager | Version information can help attackers find software that needs further review | 🟠 Medium |
| 2 | Server IP address identifiable | Nslookup resolved to `192.232.216.135` | Reveals the network location of the web service | 🟢 Low |
| 3 | HTTP technical information exposed | Curl returned headers and exposed `/wp-json/` | Assists fingerprinting and further enumeration | 🟢 Low |
| 4 | WAF technology identifiable | Wafw00f identified ModSecurity (SpiderLabs) | Reveals part of the web application's security architecture | 🟢 Low |
| 5 | DNS infrastructure information exposed | DNSRecon identified DNS, mail and service records | Helps build a broader infrastructure profile | 🟠 Medium |
| 6 | Multiple live hosts visible on local network | Zenmap identified four live hosts (example network) | Unknown or unauthorised devices may be present | 🟠 Medium |

>  These are **observations**, not confirmed vulnerabilities. The exercises were limited to information gathering and host discovery. No exploitation or vulnerability validation was performed. Exposed information such as a version number, IP address or DNS record does not by itself mean a system is vulnerable; further authorised testing would be needed to confirm any actual vulnerability.

## 6. Recommendations

1. **Review publicly exposed technology information:** regularly check what web technologies, CMS and plugins are visible externally.
2. **Keep software updated:** patch the CMS, plugins and other components and review them against current security advisories.
3. **Review HTTP headers:** remove unnecessary technical detail from responses.
4. **Review DNS records regularly:** make sure only required records and services are public.
5. **Configure and monitor the WAF:** keep ModSecurity enabled and tuned, since it already blocks naive attacks.
6. **Run regular internal network discovery:** periodically scan your own network to identify active devices.
7. **Investigate unknown devices:** verify any unexpected device found during scanning.
8. **Maintain network documentation:** keep topology and device inventories current.
9. **Test only with authorisation:** reconnaissance and scanning must stay within an approved scope.

## 7. Conclusion

In Week 2 of my Cybersecurity & Ethical Hacking internship I completed practical activities covering footprinting, reconnaissance and network scanning.

**Footprinting:** I used six Kali Linux tools against the target domain. WHOIS provided registration details, WhatWeb identified web technologies, Nslookup resolved the domain, Curl exposed HTTP headers, Wafw00f identified the WAF, and DNSRecon enumerated DNS records.

**Scanning:** I used Zenmap to identify my local network configuration, discover active hosts, collect IP and MAC addresses, and create a network topology.

**Key takeaways**

- Information gathering is a core part of cybersecurity. Before any exploitation, a lot can be learned from public information and network responses.
- Findings must be documented clearly: what was done, what was found, what it means, what risk it creates, and how to reduce it.
- Reconnaissance and scanning must always stay within an authorised scope. These activities were completed as part of an assigned educational lab.

---

<div align="center">

**— End of Report —**

</div>
