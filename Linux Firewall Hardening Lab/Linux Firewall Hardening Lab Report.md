# Linux Firewall Hardening & Attack Surface Validation Lab (Controlled Lab)

**Author:** Lawrence Omonua  
**Date:** 2026-01-23  
**Environment:** Kali Linux, Ubuntu VM, Metasploitable2 — isolated virtual lab network  
**Scope & Authorization:** This lab was performed in a controlled local environment using intentionally
vulnerable systems for educational and defensive security purposes.

---

## Executive Summary
This lab demonstrates how attack surface visibility informs defensive hardening decisions and how firewall
controls can be validated through controlled network scans. Using Kali Linux for reconnaissance, I
enumerated exposed services on both a vulnerable Metasploitable2 system and a standard Ubuntu server to
establish baseline risk. Based on this assessment, I implemented host-based firewall rules using UFW on the
Ubuntu system, enforcing least privilege by allowing only required services. Post-hardening scans confirmed
a reduced attack surface and validated the effectiveness of the firewall configuration.

---

## Lab Objectives
- Enumerate exposed services on Linux systems using Nmap
- Compare a vulnerable vs a hardened attack surface
- Implement host-based firewall rules using UFW
- Validate firewall effectiveness through controlled Nmap scans
- Demonstrate defensive decision making based on risk assessment

---

## Lab Workflow

### **System Roles**
- Kali Linux: Attacker and validation platform
- Metasploitable2: An intentionally vulnerable system used to show a large attack surface
- Ubuntu: A hardened Linux system that represents a realistic host

---

### **Baseline Reconnaissance**
Initial reconnaissance was performed from Kali Linux to see exposed services on each system

```bash
nmap -sS -p- -sV metasploitable
nmap -sS -p- -sV ubuntu
```
### **Observations**
- Metasploitable2
  - Multiple open TCP ports
  - Legacy and insecure services exposed
  - Represents a poorly secured system with a large attack surface

- Ubuntu
  - Limited number of exposed services
  - Represents a more realistic baseline system
  - Still required explicit access control decisions

This phase established context by demonstrating the difference between an insecure and controlled system

---

## Threat-Informed Decision Making
Based on the reconnaissance results:
- Most network services were unnecessary and increased risk
- SSH (Port 22) was required for administrative access
- HTTPS (Port 443) was allowed for secure web access
- All other inbound traffic was considered unnecessary and denied
This approach accurately demonstrates least-privilege principles and real-world security trade-offs

---

## Firewall Implementation
Firewall rules were implemented on the Ubuntu system to explicitly control inbound traffic
```bash
ufw default deny incoming
ufw default allow outgoing
ufw allow ssh
ufw allow https
ufw enable
```
This configuration enforced:
- Explicit ingress traffic
- Reduced exposed services
- A clear and auditable access policy

---

## Post Hardening Validation
Firewall effectiveness was validated by rescanning the Ubuntu system from the Kali Linux attacker
```bash
nmap -sS -p- ubuntu
```

| System     | Open Ports Before | Open Ports After |
| ---------- | ----------------- | ---------------- |
| **Ubuntu** | Multiple          | 22, 443 only     |

This validation confirms that firewall controls were correctly implemented and enforce the intended access policy.

---

## Risk Assessment
- Severity: Medium - exposed services increase attack surface
- Likelihood: Medium - default configurations are often deployed
- Impact: Unauthorized access, service exploitation, or lateral movement

---

## Recommended Security Controls
- Enforce host-based firewalls on all Linux servers
- Allow only required services
- Validate security controls through periodic scanning
- Document firewall rules and why they exist
- Regular reassesss exposure as system requirements change

---

## Conclusion
This lab demonstrates a defensive security workflow: assessing attack surface visibility, making
threat-informed decisions, implementing least-privilege firewall controls, and validating their
effectiveness. By contrasting a vulnerable system with a hardened Linux host, the lab highlights how
intentional configuration and verification reduce risk.
