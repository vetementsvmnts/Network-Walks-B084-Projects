<div align="center">

# `W2-PM3`  FOOTPRINTING WITH MALTEGO

**Graph-based OSINT reconnaissance · Email enumeration · Passive footprinting**

![Week](https://img.shields.io/badge/Week-2-black?style=flat-square)
![Module](https://img.shields.io/badge/Project_Module-3-black?style=flat-square)
![Tool](https://img.shields.io/badge/Maltego_Graph-4.11.3-FFB000?style=flat-square)
![Platform](https://img.shields.io/badge/Platform-Windows-0078D6?style=flat-square)
![Phase](https://img.shields.io/badge/Phase-Reconnaissance-red?style=flat-square)
![Technique](https://img.shields.io/badge/Technique-Passive_OSINT-green?style=flat-square)
![Purpose](https://img.shields.io/badge/Purpose-Education_Only-lightgrey?style=flat-square)

</div>

---

## 01  Executive Summary

This module covers the deployment of **Maltego Graph** on a Windows host and its use to perform **passive email footprinting** against the in-scope target domain `networkwalks.com`. Maltego is a link-analysis platform that models infrastructure, people and data as typed **entities** and expands them through **Transforms**, which query external data sources and return new entities to the graph.

The engagement produced a clean, repeatable workflow: provision the tooling and licence activation, seed the graph with a `Domain` entity, run email-oriented transforms from the `Utilities` hub item, and review the harvested addresses on the graph. The resulting graph contained **2 entities and 1 link**: the seed domain and the address **`info@networkwalks.com`**.

> **Authorisation.** The module brief scopes the activity to `networkwalks.com` "with due permission". All activity in this module is limited to that target and was performed for education and research purposes.

---

## 02  Objectives

| ID | Task | Outcome |
|----|------|---------|
| **Task 1** | Download and install Maltego on a Windows computer | Maltego Graph 4.11.3 installed, activated via Maltego ID, initialised to a blank graph |
| **Task 2** | Find all email addresses related to the target organisation domain `networkwalks.com` (with due permission) | Email transforms executed; `info@networkwalks.com` identified and linked to the domain entity |

---

## 03  Environment

| Component | Detail |
|-----------|--------|
| Operating system | Windows (x64) |
| Application | Maltego Graph (Desktop) **v4.11.3** |
| Installer | `MaltegoSetup.JRE64.v4.11.3.exe` (`.exe` + Java x64 package) |
| Java runtime | Eclipse Temurin JRE 17.0.8.1+1 (x64), installed by the Maltego setup (JRE 17 is the stated prerequisite) |
| Licensing | Free Maltego ID account, Online Activation (default) |
| Transform source | `Utilities` hub item |
| Privacy mode | Normal |
| Browser for login | Default system browser |
| Target | `networkwalks.com` |

---

## 04  Methodology

```text
 [Provision]        [Activate]         [Seed]            [Enumerate]        [Review]
 Download + install  Maltego ID login   Domain entity     Email transforms   Graph / output log
 JRE 17 + Maltego -> Online activation -> networkwalks.com -> from Utilities -> email entities
```

### Task 1 — Installation and Activation

| Step | Action | Notes |
|------|--------|-------|
| 1 | Download Maltego from `maltego.com` (**Resources → For Customers → Download Maltego**) | Download page offers *Maltego Graph for Windows*, `.exe + Java (x64)` |
| 2 | Run the installer as **Administrator** | The setup detects the JRE 17 prerequisite and attempts to install it automatically. The Eclipse Temurin JRE wizard runs first, followed by the Maltego setup wizard. Desktop shortcut created |
| 3 | Launch Maltego as **Administrator** and select **Maltego ID** activation | Chosen over License Key, which is intended for existing key holders |
| 4 | Select **Online Activation (Default)**, accept the EULA, then use **Browser Login** | Client waits for an authentication signal from the browser |
| 5 | On the login page choose **Create ID** and complete the registration form (email, first name, last name) | Business email recommended by the form |
| 6 | Authentication completes in the browser; return to the client | If no automatic redirect occurs, go back in the setup wizard and log in again |
| 7 | Accept the defaults through the wizard | `Utilities` transforms selected for install, default system browser, Privacy Mode: **Normal**. Finish on **Open a blank graph** |

Result: Maltego is ready to run transforms.

### Task 2 — Email Footprinting of `networkwalks.com`

1. **Seed the graph.** Search the Entity Palette for `Domain` (Infrastructure) and drag it onto the canvas.
2. **Set the value.** Double-click the entity and set *Domain Name* to `networkwalks.com`.
3. **Run transforms.** Right-click the entity, filter for `email`, and run the email-related transforms.
4. **Review.** Harvested addresses appear as linked entities; the Output pane logs each transform run.

**Email transforms exposed for the Domain entity (`Utilities`):**

| Transform | Data source |
|-----------|-------------|
| To Email address `[From whois info]` | WHOIS records |
| To Email Addresses `[PGP]` | PGP key servers |
| To Email Addresses `[Search Engine]` | Search engine results |
| To Emails @domain `[Search Engine]` | Search engine results (domain-scoped) |

The transform output log shows runs of `To Emails @domain [Search Engine]`, `To Email address [From whois info]` and `To Email Addresses [Search Engine]` against `networkwalks.com`, each returning results to the graph.

---

## 05  Findings

| # | Entity type | Value | Linked from | Discovery path |
|---|-------------|-------|-------------|----------------|
| 1 | Email address | `info@networkwalks.com` | `networkwalks.com` (Domain) | Utilities email transforms |

**Final graph state:** 2 entities, 1 link (Domain → Email).

**Observations**

- The only address surfaced is a **generic role mailbox**. No named individual accounts were returned in this run, so no personal naming convention (e.g. `first.last@`) could be inferred from this data alone.
- Each transform execution **consumes Unified Credits** (the log shows credit consumption and remaining balance per run), so transform selection has a real cost on the free tier.
- Coverage is bounded by what the queried sources index. A single result should be read as *"what these sources exposed"*, not as a complete mailbox inventory.

---

## 06  Why This Matters (Threat Perspective)

The module brief frames the value of this data for an adversary: with email and related details in hand, an attacker can **search for related vulnerabilities and launch technology-specific attacks based on relevant exploits**. The brief's example is running WordPress-focused attacks against the site, with specific attack examples covered in later sections.

From a defensive and assessment viewpoint, harvested email addresses typically feed:

- **Phishing and social engineering** target lists
- **Credential attacks** (password spraying, credential stuffing) against exposed login surfaces
- **Naming-convention inference** for account enumeration where more than one address is found
- **Further pivoting** inside Maltego into DNS, WHOIS and person/organisation entities

*(This list is analyst commentary expanding on the brief's attacker rationale.)*

---

## 07  Defensive Considerations

| Control | Rationale |
|---------|-----------|
| Use role-based mailboxes for public contact and keep staff addresses out of WHOIS and public pages | Reduces what email-harvesting transforms can return |
| Enable WHOIS privacy / redaction at the registrar | Removes a direct source for the `From whois info` transform |
| Enforce MFA and rate limiting on all internet-facing authentication | Limits the value of harvested addresses for credential attacks |
| Deploy SPF, DKIM and DMARC at enforcement | Reduces spoofing of discovered addresses |
| Run recurring phishing-awareness training and mail filtering | Mitigates the most likely use of harvested addresses |
| Periodically repeat OSINT footprinting of your own domain | Verifies actual external exposure |

*(Recommendations are analyst commentary and are not part of the source module.)*

---

## 08  Tool Notes

- Maltego was created by **Paterva** in **2008** in **Pretoria, South Africa**.
- Maltego has powered over one million investigations worldwide since 2008.
- It is used by major organisations including the FBI and INTERPOL, as well as half of the companies in the DOW 30.
- The **Community Edition (CE)** is free with limited features; paid plans unlock more results, data and commercial use.
- Results and graphs can be exported to **XLSX, CSV, images and PDF** for sharing and reporting.
- Maltego can also be installed and used on **Kali Linux**.
- Many additional transforms exist beyond the email set, for example *DNS from Domain*, *Domain Name System (DNS) Lookup [DNS]*, *Domain owner detail*, *Email addresses from Domain*, *Explore Historical Snapshots*, *Person from Domain*, *Search Historical Files*, *Search Web* and *To Details [Whois]*. The brief recommends trying them one by one for learning purposes.
- Built-in **Machines** include *Company Stalker*, *Find Wikipedia Edits* and *Footprint L1 / L2 / L3 / XXL*.

---

## 09  Evidence

Screenshots are stored in `Assets/`.

| Stage | File |
|-------|------|
| Maltego download page | `Assets/01-download-page.png` |
| Installer and JRE prerequisite | `Assets/02-installer.png` |
| Setup wizard completion | `Assets/03-setup-complete.png` |
| Maltego ID activation and login | `Assets/04-activation.png` |
| Ready-to-use blank graph | `Assets/05-blank-graph.png` |
| Domain entity set to `networkwalks.com` | `Assets/06-domain-entity.png` |
| Email transform selection | `Assets/07-email-transforms.png` |
| Final graph with harvested email | `Assets/08-final-graph.png` |

```markdown
![Final graph](Assets/08-final-graph.png)
```

---

## 10 Legal and Ethical Notice

This material is for **education and research only**. Footprinting is only legitimate when you:

- test a device or network that you own, or your lab environment;
- have written and documented permission from the owner; or
- are working as a security professional under a signed agreement with an agreed scope.

Everything outside these cases is illegal. In most jurisdictions unauthorised access is an offence even when nothing is damaged.

---

<div align="center">

`NETWORKWALKS  CYBERSECURITY & ETHICAL HACKING WEEK 2 W2-PM3`

</div>
