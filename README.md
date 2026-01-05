# 🏢 Enterprise IT Infrastructure Lab
**By Taiwo Olumide**

## 📌 Overview
This project documents my hands-on experience building a virtualized enterprise network. It covers the full lifecycle of a Windows Server environment, from initial provisioning on **VMware ESXi** to advanced **Active Directory** troubleshooting.

---

## 🏗️ Phase 1: Virtualization & Server Setup
**Goal:** Deploying the foundation for domain services.
* **Activity:** Provisioned multiple Windows Server 2022 instances.
* **Key Tasks:** Configured static IP addressing, system partitioning, and analyzed **Thin vs. Thick provisioning** for disk efficiency.
* **Skill Gained:** Understanding hardware resource allocation for virtualized servers.

---

## 🌐 Phase 2: DNS & Network Connectivity
**Goal:** Ensuring reliable name resolution.
* **Implementation:** Configured Forward and Reverse Lookup Zones.
* **Troubleshooting (The Firewall Blocker):** Resolved a server visibility issue by identifying and configuring **Windows Firewall** exceptions for ICMP and DNS traffic.
* **Advanced Fix (Restricted Dynamic Updates):** Identified that restricted updates were blocking SRV records, preventing new nodes from joining the domain. Resolved by enabling **Secure Dynamic Updates**.

---

## 🛡️ Phase 3: Active Directory & Group Policy (GPO)
**Goal:** Centralized identity and security governance.
* **Implementation:** Promoted a Domain Controller (DC) and designed a structured **OU hierarchy**.
* **Troubleshooting (The Privilege Gap):** Resolved an issue where new users couldn't log in by auditing **Security Descriptors** and Group Memberships.
* **Security Hardening (GPO):** Implemented a Password Lockout Policy. Used `gpresult /r` and `gpupdate /force` to find a logic error (Time-based vs. Manual Admin unlock) and corrected it.

---

## 🔧 Phase 4: Advanced Troubleshooting (The "Trust" Fix)
**Goal:** Recovering from critical domain failures.
* **The Problem:** Workstation `testMideComputer` failed to authenticate with a "Trust Relationship" error.
* **The Root Cause:** Identified a password de-sync between the local machine and Active Directory.
* **The Fix:** Performed a **Secure Channel Reset** by disjoining and re-joining the machine to the domain.

---

## 🐧 Phase 5: Hybrid Environment (Ubuntu Linux)
**Goal:** Managing cross-platform OS persistence.
* **The Issue:** Ubuntu VM reverted to an "uninstalled" state after shutdown.
* **The Fix:** Identified the installer was still mapped to the ISO. Completed the full disk installation and unmapped the virtual drive to ensure OS persistence.

---

## 🛠️ Commands Reference
| Tool | Command |
| :--- | :--- |
| **DNS** | `nslookup [hostname]` |
| **GPO** | `gpupdate /force` |
| **GPO Audit** | `gpresult /r` |
| **AD Trust** | `Test-ComputerSecureChannel -Verify -Repair` |
