# Offensive Security Lab — Metasploitable2 (Controlled Lab)

**Author:** Lawrence Omonua  
**Date:** 2025-09-22  
**Environment:** Kali Linux (attacker) + Metasploitable2 (target) — VirtualBox isolated network  
**Scope & Authorization:** This work was performed in an isolated, authorized lab environment against
the intentionally vulnerable Metasploitable2 VM. No unauthorized systems were targeted.

---

## Executive Summary
This lab demonstrates reconnaissance, service enumeration, and credential-based exploitation of FTP and
SSH services on Metasploitable2 using `netdiscover`, `nmap`, `Hydra`, and the Metasploit Framework. I
created short username/password wordlists, performed automated brute-force attempts, obtained interactive sessions,
and documented findings with remediation recommendations.

---

## Lab Workflow
**Setup**
- Installed and configured Kali Linux in VirtualBox.  
- (Metasploitable2 obtained separately) Configured VirtualBox NAT/host-only network so both VMs can communicate.

**FTP path**
1. Network discovery with `netdiscover` to identify devices.  
2. Service enumeration with `nmap` against the Metasploitable2 IP.  
3. Created `users.txt` via `nano` containing candidate usernames.  
4. Created `passwords.txt` via `nano` containing candidate passwords.  
5. Confirmed the lists.  
6. Brute-forced FTP using `hydra` with the created wordlists.  
7. Verified successful FTP login and captured session evidence.

**SSH path**
1. Re-used `nmap` output to confirm SSH (22/tcp) presence.  
2. Used `msfconsole` (module `auxiliary/scanner/ssh/ssh_login`) with username/password lists to test SSH logins.  
3. Set `RHOSTS`, `USER_FILE`, and `PASS_FILE` in the module, then executed the module.  
4. Identified active sessions and joined one to obtain an interactive shell.

---

## Results (summary)
Open services discovered: FTP (21/tcp), SSH (22/tcp) — confirmed via nmap.

FTP: Successful brute-force login achieved using Hydra with local wordlists (document success line in evidence/hydra_output.txt,
sanitized).

SSH: Metasploit ssh_login identified at least one valid username/password pair in the lab environment; interactive shell access
verified.

Impact: Remote access was obtainable via credential-based attacks; this demonstrates risk from default/weak credentials and
exposed remote services.

---

## Risk assessment
Severity: High — exposed remote access services with weak credentials allow unauthorized remote access, potential data exfiltration, privilege escalation, and persistence.

Likelihood: High for systems using default or weak passwords and lacking monitoring.

Impact: Potential full compromise of the host and lateral movement into connected networks.

---

## Recommended Security Changes
Disable insecure services (highest impact): Replace FTP with SFTP and remove unnecessary legacy services.

Enforce strong authentication: Implement SSH key-based authentication and disable, enable MFA for remote access if possible, require strong, unique passwords

Network access controls: Use a host based firewall and/or network ACLs to restrict SSH/FTP. Place administrative services on private networks or behind VPNs.
