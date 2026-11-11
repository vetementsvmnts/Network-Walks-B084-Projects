<div align="center">

```text
███████╗███████╗███╗   ██╗███╗   ███╗ █████╗ ██████╗ 
╚══███╔╝██╔════╝████╗  ██║████╗ ████║██╔══██╗██╔══██╗
  ███╔╝ █████╗  ██╔██╗ ██║██╔████╔██║███████║██████╔╝
 ███╔╝  ██╔══╝  ██║╚██╗██║██║╚██╔╝██║██╔══██║██╔═══╝ 
███████╗███████╗██║ ╚████║██║ ╚═╝ ██║██║  ██║██║     
╚══════╝╚══════╝╚═╝  ╚═══╝╚═╝     ╚═╝╚═╝  ╚═╝╚═╝     
```

```text
╔══════════════════════════════════════════════════════════════════╗
║  W2-PM5 :: NETWORK DISCOVERY & HOST ENUMERATION                  ║
║  target   : 10.0.0.0/24 (lab LAN)   tool : Nmap 7.91 / Zenmap    ║
║  platform : Windows 10              mode : active (host discovery)║
╚══════════════════════════════════════════════════════════════════╝
```

#  Network Scanning with Zenmap

**Network Walks · Week 2 · Project Module 5**
*Live-host discovery, MAC attribution and topology mapping of a lab subnet*

<br>

![Nmap](https://img.shields.io/badge/Nmap-7.91-0d1117?style=for-the-badge&labelColor=0d1117&color=00ff41)
![Zenmap](https://img.shields.io/badge/Zenmap-GUI-0d1117?style=for-the-badge&labelColor=0d1117&color=00ff41)
![Windows](https://img.shields.io/badge/Windows_10-0d1117?style=for-the-badge&logo=windows&logoColor=00ff41)
![Phase](https://img.shields.io/badge/Phase-Scanning_&_Discovery-0d1117?style=for-the-badge&labelColor=0d1117&color=c00000)
![MITRE](https://img.shields.io/badge/MITRE_ATT%26CK-TA0007-0d1117?style=for-the-badge&labelColor=0d1117&color=c00000)
![Status](https://img.shields.io/badge/Status-Completed-0d1117?style=for-the-badge&labelColor=0d1117&color=00ff41)

</div>


##  Executive Summary

Network discovery answers the first operational question of any assessment: **what is actually alive on this segment?** This module uses **Zenmap**, the official GUI front end for Nmap, to sweep a `/24` lab subnet, enumerate responsive hosts, attribute each to a hardware address, and export the resulting topology.

| Metric | Value |
|:---|:---:|
| Address space swept | `10.0.0.0/24` (256 addresses) |
| Live hosts | **4** (including the scanning workstation) |
| Scan duration | **2.82 s** |
| Scan type | Ping scan / host discovery only (`nmap -sn`) |
| Ports probed | **None**. Discovery only, no service enumeration |
| Host density | 4 / 256 = **1.56 %** of the address space in use |

**Key takeaways**

- A discovery-only sweep is fast, low-noise relative to a port scan, and sufficient to build an **asset baseline**.
- All three remote MAC addresses fall in **VMware OUI ranges** (`00:50:56` and `00:0C:29`), which identifies the environment as virtualised without sending a single additional probe.
- The scanner's own MAC does not appear in Nmap's output; it must be read locally with `ipconfig /all`. Missing it would under-count the inventory's hardware identifiers.

> [!NOTE]
> Addresses and results below come from the module's reference environment (`10.0.0.0/24`). On a different network the subnet, host count and MACs will differ. The method is identical.

---

##  Scope & Rules of Engagement

| Item | Detail |
|:---|:---|
| **Target** | Private lab subnet `10.0.0.0/24` |
| **Authorisation** | Owned lab / virtual environment provided for the course |
| **Technique class** | Active host discovery (`-sn`). No port scanning, version detection, OS fingerprinting or scripts |
| **Intrusive activity** | None. No authentication attempts, no exploitation |
| **Evidence handling** | Screenshots, raw output and exported topology stored in this repository |

> [!IMPORTANT]
> Active scanning generates traffic that IDS/IPS, NAC and SIEM platforms can and should detect. On a production network it requires written authorisation and an agreed scope, even when only discovery is performed.

---

##  Lab Environment

| Component | Value |
|:---|:---|
| Operating system | Windows 10 |
| Scanner | Nmap **7.91** with Zenmap GUI |
| Packet capture driver | Npcap **1.00** |
| Install path | `C:\Program Files (x86)\Nmap` |
| Bundled components | Nmap Core Files · Nmap PATH registration · Npcap · Network Performance Improvements · Zenmap · Ndiff · Ncat · Nping |
| Scanner address | `10.0.0.5` / mask `255.255.255.0` |
| Network type | Virtualised (VMware adapters) |

---

##  Methodology

```mermaid
flowchart LR
    A([Install Nmap<br/>+ Zenmap]) --> B[Identify local IP<br/>& subnet · ipconfig]
    B --> C[Zenmap<br/>Ping scan profile]
    C --> D[Parse output<br/>IPs · MACs · latency]
    D --> E[Add local MAC<br/>ipconfig /all]
    E --> F[Render topology<br/>+ legend]
    F --> G([Export PDF<br/>+ document])

    style A fill:#0d1117,stroke:#00ff41,color:#00ff41
    style B fill:#0d1117,stroke:#00ff41,color:#00ff41
    style C fill:#0d1117,stroke:#c00000,color:#ffffff
    style D fill:#0d1117,stroke:#00ff41,color:#00ff41
    style E fill:#0d1117,stroke:#00ff41,color:#00ff41
    style F fill:#0d1117,stroke:#00ff41,color:#00ff41
    style G fill:#0d1117,stroke:#c00000,color:#ffffff
```

### What a ping scan does

```text
   SCANNER                                          SUBNET 10.0.0.0/24
  10.0.0.5                                    .1   .4   .5   .19   ... 256 addrs
     │
     │  ARP who-has 10.0.0.1 ?   ─────────────►  ●
     │  ◄───────────────────────  is-at 00:50:56:E3:B3:2C
     │
     │  ARP who-has 10.0.0.4 ?   ─────────────►       ●
     │  ◄───────────────────────  is-at 00:0C:29:C0:94:8F
     │
     │  ARP who-has 10.0.0.19 ?  ─────────────►              ●
     │  ◄───────────────────────  is-at 00:50:56:E9:64:82
     │
     │  (10.0.0.5 = self, reported as up without a MAC)
     ▼
  [*] 256 IP addresses (4 hosts up) scanned in 2.82 seconds
```

On a directly connected Ethernet segment, Nmap uses **ARP requests** for host discovery when it has raw network access. This is the reason MAC addresses appear in the results, and why it is reliable even against hosts that drop ICMP.

### Command reference

| Item | Meaning |
|:---|:---|
| Zenmap profile `Ping scan` | Executes `nmap -sn <target>` |
| `-sn` | Host discovery only; skips port scanning |
| `10.0.0.0/24` | CIDR notation for 256 addresses (`255.255.255.0`) |
| `ipconfig` | Reveals local IPv4 address and subnet mask |
| `ipconfig /all` | Reveals the physical (MAC) address of local adapters |

---

##  Task 1 — Install Nmap & Zenmap

**Objective:** Download and install Zenmap from the official source on a Windows PC.

- Source: official download page at [nmap.org/download.html](https://nmap.org/download.html)
- Installer: the stable self-installer for Windows (`nmap-7.91-setup.exe`)
- Verified components: Nmap Core Files, Npcap 1.00, Zenmap GUI, Ndiff, Ncat, Nping
- Destination: `C:\Program Files (x86)\Nmap`

<div align="center">

![Nmap download page](Assets/01-nmap-download.png)
*Figure 1: Official Nmap download page and Windows installer.*

![Installer components](Assets/02-installer-components.png)
*Figure 2: Component selection during installation.*

</div>

>  **Operational note:** always download security tooling from the official project site and verify the installer's signature or checksum where provided. Trojanised copies of popular scanners are a known supply-chain lure.

---

##  Task 2 — Local IP & LAN Subnet

**Objective:** Identify the workstation's IPv4 address and LAN subnet.

```cmd
C:\> ipconfig
```

| Field | Value |
|:---|:---|
| IPv4 address | `10.0.0.5` |
| Subnet mask | `255.255.255.0` |
| Derived network | `10.0.0.0/24` |

<div align="center">

![ipconfig output](Assets/03-ipconfig.png)
*Figure 3: `ipconfig` revealing the local address and mask.*

</div>

A mask of `255.255.255.0` gives 24 network bits, so the sweep range is `10.0.0.0/24`.

---

##  Task 3 — Live Host Discovery

**Objective:** List the live hosts on the subnet using Zenmap's Ping scan.

| Zenmap field | Setting |
|:---|:---|
| Target | `10.0.0.0/24` |
| Profile | `Ping scan` |
| Resulting command | `nmap -sn 10.0.0.0/24` |

### Raw output

```text
Starting Nmap 7.91 ( https://nmap.org ) at 2021-06-11 01:08 South Africa Standard Time
Nmap scan report for 10.0.0.1
Host is up (0.00s latency).
MAC Address: 00:50:56:E3:B3:2C (VMware)
Nmap scan report for 10.0.0.4
Host is up (0.0012s latency).
MAC Address: 00:0C:29:C0:94:8F (VMware)
Nmap scan report for 10.0.0.19
Host is up (0.00036s latency).
MAC Address: 00:50:56:E9:64:82 (VMware)
Nmap scan report for 10.0.0.5
Host is up.
Nmap done: 256 IP addresses (4 hosts up) scanned in 2.82 seconds
```

<div align="center">

![Zenmap ping scan setup](Assets/04-zenmap-ping-scan.png)
*Figure 4: Target, profile and command configured in Zenmap.*

![Zenmap ping scan results](Assets/05-scan-output.png)
*Figure 5: Nmap output with the four live hosts listed in the Hosts pane.*

</div>

---

##  Tasks 4–6 — Host Count, IPs and MACs

### Task 4 · How many hosts are live?

> **4 hosts**, including the scanning PC.

### Task 5 · IP addresses of the live hosts

### Task 6 · MAC addresses of the live hosts

| # | IP address | MAC address | Vendor (OUI) | Latency | Note |
|:-:|:---|:---|:---|:---:|:---|
| 1 | `10.0.0.1` | `00:50:56:E3:B3:2C` | VMware | 0.00 s | First address in range; position consistent with a gateway or virtual switch |
| 2 | `10.0.0.4` | `00:0C:29:C0:94:8F` | VMware | 0.0012 s | Virtual machine |
| 3 | `10.0.0.19` | `00:50:56:E9:64:82` | VMware | 0.00036 s | Virtual machine |
| 4 | `10.0.0.5` | `00-0C-29-40-C0-93` | VMware | n/a | **Scanning host.** MAC read locally with `ipconfig /all` |

> The role of `10.0.0.1` is an inference from its position and address, not something the ping scan confirms. Confirm with a service scan or the hypervisor's network configuration.

---

##  Task 7 — Topology Export

**Objective:** Display the network topology, review the legend, and save it as a PDF.

**Procedure:** *Topology tab → enable Legend → review symbols → Save Graphic → file type PDF → Desktop.*

```text
                           10.0.0.5
                              ●
                              ┆
        10.0.0.19 ●┄┄┄┄┄┄┄┄┄┄ ◉ localhost ┄┄┄┄┄┄┄┄┄┄● 10.0.0.1
                              ┆
                              ●
                           10.0.0.4

        ◉ scanning host      ● discovered host      ┄ no traceroute information
```

<div align="center">

![Topology view with legend](Assets/06-topology-legend.png)
*Figure 6: Zenmap topology (fisheye) with the legend panel open.*

![Saved topology PDF](Assets/07-topology-pdf-saved.png)
*Figure 7: Topology exported to PDF on the desktop.*

</div>

 Exported artefact: [`Output/topology.pdf`](Output/topology.pdf)

**Reading the topology**

- The scanning host sits at the centre. Every discovered host is a **direct, single-hop** neighbour, with dashed edges meaning no traceroute data. This is what a flat Layer 2 segment looks like.
- Nodes are drawn green. Because this was a ping scan with no port data, the legend's colour scale (open-port counts) carries **no exposure information** here and should not be read as a statement about risk.

---

##  Technical Analysis

| Observation | Interpretation |
|:---|:---|
| Only 4 of 256 addresses respond | Small lab; the remaining 252 are unused or non-responsive. A silent host is not proof of absence |
| Latencies range from sub-millisecond to ~1.2 ms | Same virtual switch or hypervisor; no routing hops |
| Two distinct VMware OUIs (`00:50:56`, `00:0C:29`) | Mixed virtual adapter types or hypervisor platforms in one segment |
| Scanner absent a MAC in output | Nmap does not report its own MAC; collect it from the host directly |
| 2.82 s for 256 addresses | Typical of ARP-based discovery on a local segment; a routed or filtered network would be far slower and less complete |

### Limitations worth stating

- **Discovery is not enumeration.** No ports, services, OS or versions were collected, so the exposure of these hosts is unknown.
- **A host can evade discovery** if it is powered off, firewalled in a way that drops the probes, or on a different VLAN. Ping scan results are a lower bound.
- **MAC addresses are Layer 2 only.** They are visible solely on the local segment and can be spoofed, so they are an identifier, not an authentication factor.

---

## MITRE ATT&CK Mapping

| Tactic | Technique | ID | Relevance |
|:---|:---|:---:|:---|
| Discovery | Remote System Discovery | `T1018` | Enumerating live hosts on the subnet |
| Discovery | System Network Configuration Discovery | `T1016` | `ipconfig` to learn address, mask and adapter details |
| Reconnaissance | Active Scanning: Scanning IP Blocks | `T1595.001` | The external-facing analogue of this internal sweep |

---

##  Defensive Recommendations

| # | Control | Rationale | Priority |
|:-:|:---|:---|:---:|
| 1 | **Maintain an authoritative asset inventory** and reconcile it against scheduled discovery sweeps | Unknown devices are the first sign of shadow IT or a rogue host | 🔴 High |
| 2 | **Baseline and diff scans with Ndiff** (`ndiff old.xml new.xml`) | Detects new hosts and changes automatically | 🔴 High |
| 3 | **Enforce NAC / 802.1X** on access ports | Prevents unmanaged devices from joining the segment | 🔴 High |
| 4 | **Enable Dynamic ARP Inspection and DHCP snooping** | Mitigates ARP spoofing, which MAC-based trust is vulnerable to | 🟠 Medium |
| 5 | **Segment the network** (VLANs, firewalls between tiers) | Limits what a compromised host can discover | 🔴 High |
| 6 | **Alert on sweep behaviour**: bursts of ARP or ICMP to sequential addresses | Early indicator of internal reconnaissance | 🟠 Medium |
| 7 | **Restrict who may run scanners** and log their use | Prevents tooling abuse by insiders or malware | 🟡 Routine |

---

## Reproducing This Lab

**GUI path**

1. Install Nmap and Zenmap from [nmap.org](https://nmap.org/download.html).
2. Run `ipconfig` and note your IPv4 address and mask.
3. In Zenmap, set **Target** to your subnet and **Profile** to `Ping scan`, then press **Scan**.
4. Record IPs and MACs from the output; run `ipconfig /all` for your own MAC.
5. Open **Topology**, enable **Legend**, then **Save Graphic → PDF**.

**Command-line equivalent**

```cmd
:: 1. Local addressing
ipconfig
ipconfig /all

:: 2. Host discovery, saving all output formats (normal, XML, grepable)
nmap -sn 10.0.0.0/24 -oA Output\ping-scan

:: 3. Cross-check against the local ARP cache
arp -a

:: 4. Compare against a previous baseline
ndiff Output\baseline.xml Output\ping-scan.xml
```

> Replace `10.0.0.0/24` with your own subnet. Results will differ from the reference output.

---

# Repository Structure

```text
Week2/
└── W2-PM5/
    ├── README.md
    ├── Assets/
    │   ├── 01-nmap-download.png
    │   ├── 02-installer-components.png
    │   ├── 03-ipconfig.png
    │   ├── 04-zenmap-ping-scan.png
    │   ├── 05-scan-output.png
    │   ├── 06-topology-legend.png
    │   └── 07-topology-pdf-saved.png
    └── Output/
        ├── ping-scan.nmap
        ├── ping-scan.xml
        └── topology.pdf
```

---

<details>
<summary><b>🎬 Trivia: Nmap in popular culture</b></summary>

<br>

Nmap and Zenmap appear in hacking scenes in several films, including *The Matrix Reloaded*, *Ocean's 8*, *Snowden*, *Dredd* and *Elysium*. The project maintains a full list at [nmap.org/movies](https://nmap.org/movies/) and press coverage at [nmap.org/nmap_inthenews.html](https://nmap.org/nmap_inthenews.html).

</details>

---

##  References

- [Nmap Reference Guide](https://nmap.org/book/man.html)
- [Nmap download](https://nmap.org/download.html)
- [Network Walks: Zenmap practice lab](https://networkwalks.com/zenmap-network-scanning-practice-lab/)
- [MITRE ATT&CK: Discovery (TA0007)](https://attack.mitre.org/tactics/TA0007/)

---

##  Disclaimer

This repository documents work completed in a training environment for **education and defensive research only**. Scanning was performed against an owned lab subnet. Do not scan any network without the owner's written authorisation. Unauthorised scanning and access are offences in most jurisdictions.

---

<div align="center">

**Author:** Kitsana Thuekoh · Offensive Security Researcher
[![GitHub](https://img.shields.io/badge/GitHub-vetementsvmnts-0d1117?style=for-the-badge&logo=github&logoColor=00ff41)](https://github.com/vetementsvmnts)

`>_ you can't defend what you haven't discovered.`

</div>
