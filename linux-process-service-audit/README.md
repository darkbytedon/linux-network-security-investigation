# Linux Process and Service Security Audit

## Project Overview

This project documents a practical Linux security investigation conducted on Ubuntu running under Windows Subsystem for Linux 2 (WSL2).

The investigation focused on identifying running processes, reviewing system services, inspecting listening network sockets, examining network interfaces, and checking failed systemd services.

The objective was to understand normal Linux system activity, identify potentially unusual network exposure, and document security observations using command-line investigation techniques.

## Audit Environment

| Item | Details |
|---|---|
| Operating System | Ubuntu |
| Environment | Windows Subsystem for Linux 2 (WSL2) |
| Init System | systemd |
| Audit Type | Linux Process and Service Security Audit |
| Project | 60 Days of Cybersecurity Learning |
| Day | 22 |

## Investigation Objectives

- Identify running processes and their resource usage.
- Understand process IDs (PID) and parent process IDs (PPID).
- Review active systemd services.
- Identify failed services.
- Examine listening TCP and UDP sockets.
- Investigate DNS listeners and test DNS resolution.
- Inspect network interfaces and routing information.
- Identify potential security concerns and recommend improvements.

## Tools and Commands Used

| Tool | Purpose |
|---|---|
| `ps aux` | Inspect running processes |
| `pstree -p` | Examine process relationships |
| `systemctl` | Review services and service status |
| `ss -tulnp` | Inspect listening network sockets |
| `ip -br addr` | Inspect network interfaces |
| `ip route` | Examine routing information |
| `resolvectl` | Inspect DNS configuration |
| `dig` | Test DNS resolution |

## Key Findings

### 1. Process and Service Investigation

Thirteen running systemd services were recorded, including cron, rsyslog, chrony, and systemd-resolved.

The process investigation also identified standard system processes and their parent-child relationships.

### 2. Failed Service Investigation

The command `systemctl --failed --no-pager` reported zero failed systemd units at the time of testing.

### 3. Listening Socket Investigation

DNS listeners were observed on:

- `127.0.0.53`
- `127.0.0.54`
- `10.255.255.254`

The process owner associated with the listener on `10.255.255.254` was not identified from the collected socket output.

### 4. DNS Investigation

A test query was performed against `10.255.255.254` using:

`dig @10.255.255.254 example.com`

The query returned a successful `NOERROR` response containing two A records.

This demonstrated successful DNS resolution but did not independently establish the security of the DNS configuration.

### 5. Network Investigation

The investigation identified:

- Loopback interface: `lo`
- Network interface: `eth0`
- Interface address: `172.18.49.100/20`
- Default gateway: `172.18.48.1`

The environment was operating within WSL2, so the findings do not represent a complete assessment of the Windows host.

## Security Recommendations

1. Investigate unexpected processes and listening sockets.
2. Disable unnecessary services where appropriate.
3. Restrict network listeners to intended interfaces.
4. Review services operating with elevated privileges.
5. Monitor unusual process activity and resource consumption.
6. Review DNS configuration against the system's intended networking design.
7. Repeat security audits periodically and compare results.

## Limitations

This was a controlled educational investigation conducted in Ubuntu WSL2.

It was not a complete penetration test, Windows host security assessment, or malware investigation.

No confirmed compromise was identified from the evidence collected.

## Skills Demonstrated

- Linux process monitoring.
- Linux service management.
- Process and service analysis.
- Network socket investigation.
- DNS configuration analysis.
- Network interface inspection.
- Security finding documentation.
- Risk interpretation and remediation recommendations.

## Full Technical Report

For the complete investigation, commands, observations, findings, and conclusions, see:

[DAY 22 - Linux Process and Service Security Audit](DAY22-LINUX-PROCESS-SERVICE-AUDIT.md)

## Disclaimer

This project was conducted for educational and professional development purposes in a controlled local environment. The findings represent observations from the documented lab and should not be interpreted as a security certification or an assessment of a third-party system.

---

**Author:** Cybersecurity Learning Portfolio  
**Project:** 60 Days of Cybersecurity Learning  
**Topic:** Linux Process and Service Security Audit
