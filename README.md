# 🏢 Enterprise IT Infrastructure Lab

**Author:** Taiwo Olumide  
**Domain:** `nico.local`  
**Hypervisor:** VMware ESXi 7.0  
**Network:** `192.168.0.0/24`

---

## 📌 Project Overview

This lab simulates a real enterprise IT environment built from scratch using VMware ESXi.

The environment includes:

- Windows Server 2019 Domain Controllers  
- Active Directory Domain Services (AD DS)  
- Active Directory–Integrated DNS  
- Group Policy  
- Windows 10 domain-joined clients  
- Real troubleshooting scenarios  

The objective was to design, deploy, break, fix, and validate a working enterprise infrastructure.

---

## 🗺️ Network Architecture

### Topology Diagram

![Network Diagram](images/network-diagram.png)

**Flow:**  
Physical Router → vmnic1 → vSwitch0 → ESXi Host → Virtual Machines

---

### Machines

| Machine | Role | IP Address |
|----------|------|------------|
| nicoDC | Primary Domain Controller | 192.168.0.129 (Static) |
| SecondaryDC | Secondary Domain Controller | Internal |
| COMP1 | Windows 10 Client | 192.168.0.171 |
| LDAP2ConnectionAgent | LDAP Test Machine | 192.168.0.110 |
| Other Lab VMs | Testing | Internal |

---

## 🛠️ Technologies Used

- VMware ESXi 7.0 Update 2
- Windows Server 2019
- Windows 10
- Active Directory Domain Services (AD DS)
- Active Directory–Integrated DNS
- Group Policy Objects (GPO)
- PowerShell
- VMware vSwitch Networking
- Thin-Provisioned Storage

---

## 🚀 Deployment Process

### 1️⃣ VM Provisioning (ESXi)

Provisioned VMs with allocated CPU, RAM, and thin-provisioned storage.

![ESXi Provisioning](images/esxi-vm-provisioning.png)

---

### 2️⃣ Install AD DS Role

Installed Active Directory Domain Services on Windows Server.

![AD DS Install](images/ad-ds-install.png)

---

### 3️⃣ Promote to Domain Controller

Promoted server to Domain Controller for `nico.local`.

![DC Promotion](images/dc-promotion.png)

---

### 4️⃣ Domain Operational

Server successfully running as Domain Controller.

![Domain Running](images/nicodc-domain.png)

---

## 🌐 DNS Configuration

Configured Forward and Reverse Lookup Zones.

### Forward Lookup Zone

![DNS Forward Lookup](images/dns-forward-lookup.png)

---

### Host A Records

![DNS Host Records](images/dns-host-a-records.png)

---

### Zones Running

![DNS Zones Running](images/dns-zones-running.png)

---

### Reverse Lookup Zone

![DNS Reverse Lookup](images/dns-reverse-lookup.png)

---

## 💻 Client Domain Join

Windows 10 client successfully joined to the domain.

### Client Desktop

![Client Desktop](images/comp1-desktop.png)

---

### Domain Join Confirmation

![Domain Join](images/comp1-domain-join.png)

---

## 🏢 Active Directory Structure

Created department-based OU structure:

- Finance
- HR
- IT
- Sales
- Servers
- Service Accounts

### OU Structure

![OU Structure](images/ad-ou-structure.png)

---

### Computers Container

![ADUC Computers](images/aduc-computers.png)

---

## 🔍 Troubleshooting Scenarios

Real issues were triggered and resolved.

### Network Troubleshooting

![Network Troubleshooting](images/network-troubleshooting.png)

### Issues Resolved

- DNS request timed out (Firewall blocking Port 53 and ICMP)
- Secure Channel trust failure
- Broken domain join
- Ubuntu boot loop (ISO mapping issue)

---

## ✅ Validation

- Domain Controllers operational
- DNS Forward & Reverse zones running
- A and PTR records verified
- Client successfully joined to domain
- GPO applied and verified with:

```
gpresult /r
```

- Secure Channel verified with:

```
Test-ComputerSecureChannel -Verify
```

---

## 🧠 Lessons Learned

- DNS must work before anything else.
- Domain Controllers require static IPs.
- Trust relationship failures are Secure Channel issues.
- Firewall rules can silently block services.
- Thin provisioning requires monitoring.
- OU structure should be planned early.

---

## 📂 Repository Structure

```
Enterprise-IT-Infrastructure-Lab/
├── docs/
├── images/
├── scripts/
├── README.md
└── LICENSE
```

---

## 🎯 Skills Demonstrated

- Hypervisor administration (Type-1)
- Windows Server deployment
- Active Directory design
- DNS configuration and troubleshooting
- Group Policy implementation
- Domain join operations
- Secure Channel repair
- Enterprise OU structure planning
