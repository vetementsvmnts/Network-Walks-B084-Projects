```
██╗    ██╗██████╗       ██████╗ ███╗   ███╗██████╗ 
██║    ██║╚════██╗      ██╔══██╗████╗ ████║╚════██╗
██║ █╗ ██║ █████╔╝█████╗██████╔╝██╔████╔██║ █████╔╝
██║███╗██║ ╚═══██╗╚════╝██╔═══╝ ██║╚██╔╝██║ ╚═══██╗
╚███╔███╔╝██████╔╝      ██║     ██║ ╚═╝ ██║██████╔╝
 ╚══╝╚══╝ ╚═════╝       ╚═╝     ╚═╝     ╚═╝╚═════╝ 
```

<div align="center">

# Credential Recovery & Document Hash Cracking
### Offline Password Attacks Against Protected PDF Containers

[![Module](https://img.shields.io/badge/Module-W3--PM2-black?style=for-the-badge)](.)
[![Tooling](https://img.shields.io/badge/Tooling-John%20the%20Ripper-red?style=for-the-badge)](.)
[![Platform](https://img.shields.io/badge/Platform-Kali%20Linux-557C94?style=for-the-badge&logo=kalilinux)](.)
[![Status](https://img.shields.io/badge/Status-Completed-brightgreen?style=for-the-badge)](.)

</div>

---

## Overview

This module documents an offline password-recovery exercise against a
password-protected PDF container, performed via two independent attack
paths to demonstrate equivalence between command-line and web-based
cracking workflows:

| Path | Tooling | Environment |
|------|---------|-------------|
| **A** | `pdf2john` + John the Ripper | Kali Linux (CLI) |
| **B** | Networkwalks Hash Calculator + Password Cracker | Browser (Web) |

The objective is standard in offensive security engagements: when a
client hands over an encrypted document for scoped review, the assessor
must recover the key (or demonstrate how trivially it can be recovered)
before the document's contents can be assessed for sensitive data
exposure.

---

##  Background

**John the Ripper (JtR)** is an open-source password-cracking engine
that supports dictionary, brute-force, and rule-based mutation attacks
against a wide range of hash formats. It does not attack the PDF
directly — it attacks a **hash** derived from the PDF's encryption
metadata.

**Hashing**, in this context, refers to the one-way transformation PDF
encryption uses to validate a password without storing it in plaintext.
`pdf2john` reverse-engineers the PDF's `/Encrypt` dictionary (filter,
revision, key length, O/U strings) and serializes it into a crackable
hash format (`$pdf$...`). JtR then replays candidate passwords through
the same KDF the PDF spec defines, comparing output against the stored
digest until a match is found.

---

## Section 1 — W3-PM1: John the Ripper (CLI Method)

### Procedure

```bash
# Navigate to the working directory
cd ~/Downloads

# Extract the crackable hash from the PDF's encryption dictionary
pdf2john My\ Locked\ PDF1.pdf > hash1.txt

# Run the cracking engine against the extracted hash
john hash1.txt

# Display the recovered credential
john --show hash1.txt
```

### Evidence



```

```

### Result

| Field | Value |
|---|---|
| Target file | `My Locked PDF1.pdf` |
| Hash format | `$pdf$` (PDF encryption hash) |
| Attack mode | Wordlist / default ruleset |
| Recovered password | `password1` |
| Time to crack | < 1 second |

**Finding:** The hash was extracted using `pdf2john` and cracked using
John the Ripper. The password was recovered as `password1` — a dictionary
word with a trivial numeric suffix, placing it in the first few thousand
candidates of any standard wordlist.

---

## Section 2 — W3-PM2: Networkwalks Web Tools (Browser Method)

### Procedure

1. Navigate to the [Hash Calculator](https://networkwalks.com/hash-calculator/)
2. Upload `My Locked PDF1.pdf` via the **File** tab
3. Copy the generated `$pdf$...` hash
4. Navigate to the [Password Cracker](https://networkwalks.com/password-cracker/)
5. Paste the hash and initiate the attack

### Evidence



### Result

| Field | Value |
|---|---|
| Extraction method | Browser-based hash calculator |
| Cracking method | Browser-based password cracker |
| Recovered password | `password1` |

**Finding:** The locked PDF was uploaded to the Hash Calculator to
extract the hash, and the Password Cracker recovered the password
`password1` without any local tooling installation.

---

##  Section 3 — Verification

The recovered credential was validated against the original artifact:

1. `My Locked PDF1.pdf` opened in a PDF reader
2. Password prompt satisfied with `password1`
3. Document decrypted successfully, confirming the recovered credential
   was correct (not a hash collision or false positive)

> 📸 *Insert screenshot of the unlocked PDF / success flag here*

---

##  Conclusion

This lab demonstrates two valid paths to the same outcome — a local,
offline CLI workflow (`pdf2john` + JtR) and a web-based equivalent — and
confirms they converge on an identical result when the underlying
cryptographic weakness is the same: a short, dictionary-based password.

**Key takeaway:** `password1` was recovered in under a second regardless
of tooling. This is not a tooling strength — it is a **password policy
failure**. In a real engagement, this finding would be reported as:

> *"Document-level encryption was in use, but the chosen password did
> not provide meaningful protection against offline brute-force attack.
> Recommend enforcement of a minimum-entropy passphrase policy for any
> document containing sensitive or regulated data."*

---

<div align="center">

**Module:** W3-PM1 / W3-PM2 — Networkwalks Training Program
**Classification:** Training Lab — No real-world targets or data involved

</div>
