# 🏢 Enterprise IT Infrastructure Lab

**Author:** Taiwo Olumide &nbsp;|&nbsp; **Domain:** `nico.local` &nbsp;|&nbsp; **Hypervisor:** VMware ESXi 7.0 &nbsp;|&nbsp; **Subnet:** `192.168.0.0/24`

---

## 📌 Project Overview

This project documents the design, deployment, and troubleshooting of a fully virtualized enterprise network built from scratch on bare-metal hardware using VMware ESXi. The environment replicates real-world IT infrastructure: a promoted Windows Server 2019 Domain Controller running Active Directory, DNS, and Group Policy, with Windows 10 client machines successfully joined to the `nico.local` domain. The lab was extended to include a Secondary DC for redundancy, a structured OU hierarchy modeled after a multi-department organization, and documented resolution of production-grade failures including Secure Channel breakdowns and DNS firewall blocks.

---

## 🗺️ Architecture Diagram

![Network Architecture Diagram](images/network-diagram.svg)

> **Topology at a glance:**
> Physical Router → vmnic1 → vSwitch0 → ESXi Host → VMs on `192.168.0.0/24`
>
> | Machine | Role | IP Address |
> |---|---|---|
> | nicoDC | Primary Domain Controller | `192.168.0.129` (static) |
> | SecondaryDC | Backup Domain Controller | Internal |
> | COMP1 | Domain-Joined Workstation | `192.168.0.171` |
> | LDAP2ConnectionAgent | LDAP Test Machine | `192.168.0.110` |
> | mideTestServer | Additional Server VM | Internal |
> | Supporttrainee / testRSConnectionAgent | Lab Workstations | Internal |

---

## 🛠️ Technologies Used

| Category | Technology |
|---|---|
| Hypervisor | VMware ESXi 7.0 Update 2 |
| Server OS | Windows Server 2019 Standard Evaluation |
| Client OS | Windows 10 |
| Directory Services | Active Directory Domain Services (AD DS) |
| DNS | Active Directory-Integrated DNS (Forward + Reverse Lookup Zones) |
| Identity & Policy | Group Policy Objects (GPO), Organizational Units (OUs) |
| Networking | VMware vSwitch, Static IP addressing, 192.168.0.0/24 |
| Management Tools | Server Manager, ADUC, DNS Manager, PowerShell |
| Storage | VMware Thin Provisioned VMDK on `datastore1` (68 GB) |

---

## 💡 Skills Demonstrated

- **Type-1 Hypervisor Administration** — Provisioning and managing multiple VMs on VMware ESXi with resource allocation (vCPU, RAM, thin-provisioned storage)
- **Windows Server Administration** — Installing roles (AD DS, DNS), promoting Domain Controllers, configuring Server Manager
- **Active Directory Design** — Building a structured OU hierarchy across Users, Computers, Servers, and Service Accounts reflecting a real multi-department org
- **DNS Management** — Configuring Forward and Reverse Lookup Zones, verifying SOA/NS/A/PTR records, and resolving service discovery issues with `dnscmd`
- **Group Policy** — Creating and applying Password Lockout Policies, verifying policy application with `gpresult /r`
- **Network Troubleshooting** — Diagnosing and resolving firewall blocks (Port 53, ICMP), domain trust failures, and Secure Channel desync using PowerShell
- **Domain Operations** — Joining Windows 10 clients to an AD domain, managing computer accounts, and resetting broken trust relationships
- **Cross-Platform Management** — Working with multiple server and client VMs simultaneously within a shared virtual network

---

## 🎯 Lab Objectives

1. Deploy a Type-1 hypervisor (VMware ESXi) on physical hardware and provision multiple VMs
2. Install and configure Windows Server 2019 as a Primary Domain Controller for `nico.local`
3. Deploy a Secondary Domain Controller for redundancy and failover
4. Configure Active Directory-Integrated DNS with Forward and Reverse Lookup Zones
5. Design a department-aligned OU hierarchy (Finance, HR, IT, Sales) for Users, Computers, and Servers
6. Join Windows 10 client machines to the domain and validate AD authentication
7. Implement and verify Group Policy (Password Lockout Policy) across the domain
8. Diagnose and resolve real production-grade failures: DNS firewall blocks, Secure Channel trust failures, and OS boot issues

---

## ✅ Results / Validation

**Domain Controller Promotion**
- `nicoDC` successfully promoted to Primary DC; `nico.local` domain operational
- SecondaryDC deployed and joined as a backup Domain Controller

**DNS**
- Forward Lookup Zone (`nico.local`) and Reverse Lookup Zone (`0.168.192.in-addr.arpa`) both running with status **Running** in DNS Manager
- Host A records confirmed: `nicodc → 192.168.0.129`, `COMP1 → 192.168.0.171`, `LDAP2Connectionagent → 192.168.0.110`
- PTR records verified in Reverse Lookup Zone
- SRV record registration verified via `dnscmd`

**Client Domain Join**
- `COMP1` (Windows 10) successfully joined to `nico.local` — confirmed by "Welcome to the nico.local domain" dialog
- Computer account for `COMP1` visible in Active Directory Users and Computers

**Active Directory OU Structure**
- Full department OU hierarchy created and verified in ADUC:
  - `OU_Users` → Finance_Users, HR_Users, IT_Users, Sales_Users
  - `OU_Computers` → Finance, HR, IT, Sales Computers
  - `OU_Servers` → App_Servers, DB_Servers
  - `OU_ServiceAccounts`

**Group Policy**
- Password Lockout Policy GPO created, linked, and verified with `gpresult /r`

**Troubleshooting Resolved**
- DNS "Request Timed Out" → fixed via custom Windows Firewall Inbound Rules (Port 53 + ICMP)
- Secure Channel / Trust Relationship failure → resolved by disjoining and re-joining to domain
- Ubuntu VM boot loop → fixed by unmapping installer ISO from virtual drive

---

## 🔧 Commands Reference

| Tool | Command | Purpose |
|---|---|---|
| DNS Lookup | `nslookup [hostname]` | Verify DNS name resolution |
| DNS Record Check | `dnscmd /enumrecords nico.local @ /type A` | List A records in zone |
| GPO Audit | `gpresult /r` | Verify applied Group Policies |
| Secure Channel Test | `Test-ComputerSecureChannel -Verify` | Check domain trust health |
| Secure Channel Fix | `Test-ComputerSecureChannel -Repair` | Repair broken domain trust |
| AD Module | `Import-Module ActiveDirectory` | Enable AD PowerShell cmdlets |

---

## 📚 Lessons Learned

**DNS must be right before anything else works.** Early failures with `nslookup` traced back to Windows Firewall silently blocking Port 53 and ICMP — not a DNS misconfiguration. The fix required creating explicit Inbound Rules, not disabling the firewall entirely. This reinforced that in enterprise environments, security controls can mask configuration issues.

**Static IPs are non-negotiable for infrastructure.** Assigning static IPs to `nicoDC` (`192.168.0.129`) immediately eliminated service disruption from DHCP lease changes. Domain Controllers and DNS servers must never rely on dynamic addressing.

**Trust Relationship failures are a Secure Channel problem, not a credentials problem.** When `COMP1` threw a "Trust Relationship" error, the instinct might be to reset the user password. The real cause was a password de-sync between the machine account and AD. The correct fix is a Secure Channel reset — either via `Test-ComputerSecureChannel -Repair` or by disjoining and rejoining the domain.

**OU design matters from day one.** Building department-aligned OUs (Finance, HR, IT, Sales) upfront makes GPO targeting, delegation, and auditing dramatically simpler at scale. Retrofitting OU structure in a live environment is painful.

**Thin provisioning buys flexibility, not infinity.** Thin-provisioned virtual disks defer storage allocation but the physical host has a ceiling. Monitoring actual datastore usage against the 68 GB host pool is a real operational concern when running 23+ VMs simultaneously.

---

## 📁 Repository Structure

```
Enterprise-IT-Infrastructure-Lab/
├── docs/               # Phase-by-phase technical documentation
├── images/             # Screenshots and network diagram
│   └── network-diagram.svg
├── scripts/            # PowerShell scripts used in the lab
├── README.md
└── LICENSE
```

---

## 📸 Screenshots

| Phase | Screenshot |
|---|---|
| ESXi VM Provisioning | `images/esxi-vm-provisioning.png` |
| AD DS Installation Progress | `images/ad-ds-install.png` |
| DC Promotion | `images/dc-promotion.png` |
| nicoDC joined to nico.local | `images/nicodc-domain.png` |
| DNS Forward Lookup Zone | `images/dns-forward-lookup.png` |
| DNS Forward Zones (Running) | `images/dns-zones-running.png` |
| DNS Reverse Lookup Zone | `images/dns-reverse-lookup.png` |
| COMP1 Domain Join Success | `images/comp1-domain-join.png` |
| Active Directory OU Structure | `images/ad-ou-structure.png` |
| ADUC Computers Container | `images/aduc-computers.png` |

---

*Built as a hands-on home lab to simulate real enterprise IT environments. All configurations, troubleshooting steps, and resolutions reflect actual work performed on live VMs.*
