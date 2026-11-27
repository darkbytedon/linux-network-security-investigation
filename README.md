# Linux Security Investigation Portfolio

## Overview

This repository contains practical Linux security investigations conducted using Ubuntu under Windows Subsystem for Linux 2 (WSL2).

The projects demonstrate hands-on experience in Linux network analysis, access control, privilege management, process monitoring and service security.

The work forms part of a structured 60 Days of Cybersecurity Learning programme.

## Portfolio Projects

### 1. Day 12 – Linux Network and Security Investigation

**Focus:** Linux networking, DNS, listening services, active connections and firewall configuration.

Activities included:
- Examining network interfaces and routing tables.
- Investigating DNS resolution and connectivity.
- Identifying listening ports and associated processes.
- Reviewing active TCP and UDP connections.
- Inspecting UFW firewall configuration.

Selected observations:
- Ubuntu network interface: eth0.
- IPv4 address: 172.18.49.100/20.
- Default gateway: 172.18.48.1.
- DNS resolver: 10.255.255.254.
- UFW was installed but reported inactive.
- No user-added UFW rules were reported.

[Read the complete Day 12 technical report](DAY12-NETWORK-SECURITY-REPORT.md)

### 2. Linux ACL Security Audit

**Focus:** Linux Access Control Lists, effective permissions and access remediation.

Activities included:
- Inspecting file and directory ACLs.
- Testing named-user permissions.
- Investigating directory traversal restrictions.
- Examining ACL masks.
- Removing an unnecessary named-user ACL entry.
- Verifying the resulting configuration.

[View the ACL project README](linux-acl-security-audit/README.md)

[Read the complete ACL technical report](linux-acl-security-audit/LINUX-ACL-SECURITY-AUDIT.md)

### 3. Day 21 – Linux Privilege and Permissions Security Audit

**Focus:** Linux permissions, privileged executables and administrative access.

Activities included:
- Reviewing sensitive file permissions.
- Inspecting SUID and SGID executables.
- Investigating sticky-bit directories.
- Searching selected paths for world-writable files and directories.
- Examining Linux capabilities.
- Reviewing sudo privileges.
- Applying the principle of least privilege.

[View the Day 21 project README](linux-privilege-security-audit/README.md)

[Read the complete Day 21 technical report](linux-privilege-security-audit/DAY21-LINUX-PRIVILEGE-SECURITY-AUDIT.md)

### 4. Day 22 – Linux Process and Service Security Audit

**Focus:** Running processes, systemd services and listening network sockets.

Activities included:
- Inspecting running processes and process relationships.
- Reviewing active system services.
- Checking failed systemd units.
- Investigating TCP and UDP listening sockets.
- Examining DNS listeners.
- Inspecting network interfaces and routes.

[View the Day 22 project README](linux-process-service-audit/README.md)

[Read the complete Day 22 technical report](linux-process-service-audit/DAY22-LINUX-PROCESS-SERVICE-AUDIT.md)

## Technical Tools and Commands

| Tool | Investigation Purpose |
|---|---|
| ip | Network interfaces and routing |
| ss | Listening sockets and connections |
| ping | Connectivity testing |
| nslookup / dig | DNS investigation |
| ps | Process inspection |
| pstree | Process relationships |
| systemctl | Service management and review |
| getfacl / setfacl | ACL investigation and management |
| find | Filesystem permission searches |
| chmod / stat | Permission and metadata analysis |
| getcap / getpcaps | Linux capability investigation |
| sudo | Administrative privilege review |
| UFW | Firewall configuration inspection |

## Security Assessment Approach

The investigations follow a practical security workflow:

1. Define the investigation objective.
2. Collect system and configuration information.
3. Inspect permissions, processes, services or network activity.
4. Interpret observations in their environment.
5. Identify potential security concerns.
6. Recommend appropriate remediation.
7. Verify changes where remediation is performed.
8. Document findings and limitations.

## Skills Demonstrated

- Linux system administration and security analysis.
- Network configuration and DNS investigation.
- TCP and UDP socket inspection.
- Process and service monitoring.
- File permission and ACL auditing.
- Privilege and capability analysis.
- Firewall configuration review.
- Security finding documentation.
- Technical investigation and evidence interpretation.

## Environment

| Item | Details |
|---|---|
| Operating System | Ubuntu |
| Environment | Windows Subsystem for Linux 2 |
| Shell | Bash |
| Version Control | Git |
| Hosting | GitHub |
| Project Type | Educational security investigations |

## Important Limitations

These projects were conducted in a controlled educational WSL2 environment.

They are not presented as penetration tests or security assessments of actual client systems. WSL2 observations should not automatically be generalized to a production Linux server or the complete Windows host.

Findings are based on the commands and evidence documented in the individual reports.

## Purpose

This portfolio demonstrates practical cybersecurity learning, investigation methodology, technical documentation and evidence-based security reasoning.

---

**Author:** Cybersecurity Learning Portfolio  
**Programme:** 60 Days of Cybersecurity Learning  
**Repository:** Linux Network and Security Investigation
