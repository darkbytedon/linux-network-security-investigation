# Day 22: Linux Process and Service Security Audit

## 1. Executive Summary

This audit examined running processes, system services, listening network sockets, network interfaces, and failed services on an Ubuntu system running under Windows Subsystem for Linux 2 (WSL2).

The objective was to identify unexpected processes, understand active services, review listening ports, and assess potential network exposure.

The investigation identified expected system services, including cron, rsyslog, chrony, and systemd-resolved. No failed systemd services were reported during the audit.

DNS listeners were identified on loopback addresses and on the WSL2 loopback address 10.255.255.254. The DNS listener on 10.255.255.254 responded successfully to a test query. Its owning process was not identified from the available socket output.

No confirmed compromise was identified. This was a targeted educational security audit, not a complete penetration test.

## 2. Audit Environment

- Operating system: Ubuntu on WSL2
- Hostname: dondada
- Kernel: 6.18.58.1-microsoft-standard-WSL2
- User account: dondon
- Init system: systemd
- Audit type: Linux process and service security investigation

## 3. Audit Objectives

The audit aimed to:

1. Identify running processes.
2. Review active system services.
3. Examine listening TCP and UDP sockets.
4. Identify network interfaces and addresses.
5. Check for failed services.
6. Interpret potential security exposure.
7. Document findings and recommendations.

## 4. Commands Used

### System information

```bash
hostname
uname -r
```

### Running processes

```bash
ps aux
ps aux --sort=-%cpu | head -10
ps aux --sort=-%mem | head -10
pstree -p
```

### Running services

```bash
systemctl --type=service --state=running --no-pager
systemctl --failed --no-pager
systemctl list-unit-files --type=service
```

### Listening sockets

```bash
sudo ss -tulnp
```

### Network interfaces and routes

```bash
ip -br addr
ip route
```

### DNS investigation

```bash
resolvectl status
dig @10.255.255.254 example.com
```

## 5. Running Process Investigation

The `ps aux` command was used to examine running processes.

Examples of observed processes included:

- systemd (PID 1)
- cron
- dbus-daemon
- rsyslogd
- chronyd
- systemd-resolved
- systemd-udevd
- systemd-logind
- unattended-upgrades

The process tree showed systemd as PID 1 and the parent of many system services.

The highest CPU readings briefly included the `ps` and `head` commands themselves. These were short-lived command executions and did not establish persistent high CPU usage.

### Security interpretation

Process names and resource usage should be examined together. An unfamiliar process is not automatically malicious.

A security analyst should investigate its executable path, owner, parent process, command arguments, network activity, and package ownership where applicable.

## 6. System Service Investigation

The systemd service inventory showed 13 running services:

- chrony.service
- cron.service
- dbus.service
- getty@tty1.service
- networkd-dispatcher.service
- polkit.service
- rsyslog.service
- systemd-journald.service
- systemd-logind.service
- systemd-resolved.service
- systemd-udevd.service
- unattended-upgrades.service
- user@1000.service

These services provide system functions such as scheduling, logging, time synchronization, DNS resolution, device management, and user session management.

The names were consistent with normal Ubuntu system operations. However, service names alone cannot establish that a service is trustworthy.

### Failed services

The command:

```bash
systemctl --failed --no-pager
```

reported:

```text
0 loaded units listed.
```

No failed systemd units were reported at the time of the audit.

## 7. Listening Port Investigation

The command:

```bash
sudo ss -tulnp
```

was used to inspect listening TCP and UDP sockets.

The investigation identified:

| Port | Protocol | Address | Associated service / observation |
|---|---|---|---|
| 53 | UDP | 127.0.0.53 | systemd-resolved |
| 53 | UDP | 127.0.0.54 | systemd-resolved |
| 53 | TCP | 127.0.0.53 | systemd-resolved |
| 53 | TCP | 127.0.0.54 | systemd-resolved |
| 53 | TCP/UDP | 10.255.255.254 | Process owner not displayed |
| 323 | UDP | 127.0.0.1 and ::1 | chronyd |

### DNS observations

DNS listeners were found on local loopback addresses and on 10.255.255.254.

A DNS query was performed:

```bash
dig @10.255.255.254 example.com
```

The response returned NOERROR and two A records.

This confirmed that the DNS endpoint successfully processed the test query. It did not establish that the DNS service was secure or that its configuration was fully correct.

The socket output did not display a process owner for the 10.255.255.254 listener. Its ownership therefore remained unresolved during this audit.

### Security interpretation

Port 53 is used for DNS communication. DNS listeners should be understood in relation to their addresses and intended purpose.

A listener bound to 127.0.0.1 is restricted to local loopback access. A listener bound to 0.0.0.0 may accept connections through available IPv4 interfaces, subject to firewall and network configuration.

The audit did not identify a listener on 0.0.0.0:8080. That address and port were discussed as a security-learning scenario, not an observed finding.

## 8. Network Interface Investigation

The command:

```bash
ip -br addr
```

identified these interfaces:

- lo: 127.0.0.1/8 and 10.255.255.254/32
- eth0: 172.18.49.100/20

The default route was:

```text
default via 172.18.48.1 dev eth0
```

The 10.255.255.254 address was assigned to the loopback interface, not eth0.

The address was therefore not evidence by itself of an externally exposed DNS service.

The system was operating inside WSL2, so its network configuration should not be treated as a direct representation of the Windows host's complete network exposure.

## 9. DNS Configuration Observations

The `resolvectl status` command showed:

- Current DNS server: 10.255.255.254
- DNSSEC: no/unsupported
- LLMNR: disabled
- mDNS: disabled
- DNS over TLS: disabled
- resolv.conf mode: foreign

The foreign mode indicates that systemd-resolved recognized an externally managed resolv.conf arrangement.

DNSSEC was not shown as enabled or supported in the active configuration. This was recorded as a configuration observation, not evidence of DNS interception or compromise.

## 10. Security Findings

### Finding 1: Expected system services

The running service inventory contained recognizable Ubuntu system services.

Assessment: No immediately suspicious service was identified from the names alone.

### Finding 2: DNS listener on 10.255.255.254

A DNS listener was identified on the loopback-assigned address 10.255.255.254. The process owner was not displayed in the available socket output.

Assessment: Requires further attribution if a deeper investigation is necessary. No compromise was established.

### Finding 3: No failed systemd units

The failed-service check reported zero failed units.

Assessment: No failed systemd services were reported during the audit.

### Finding 4: WSL2 network environment

The system used a WSL2 virtual network interface and a loopback DNS address.

Assessment: Findings must be interpreted within the WSL2 environment and should not be generalized to the complete Windows host.

## 11. Recommendations

1. Investigate the ownership and purpose of unexpected listening sockets.
2. Disable unnecessary services where appropriate.
3. Restrict network listeners to the required interfaces.
4. Review services running with elevated privileges.
5. Monitor unexpected process execution and unusual resource consumption.
6. Review DNS configuration according to the system's intended networking design.
7. Repeat the audit periodically and compare results to identify changes.

## 12. Limitations

This investigation was conducted in Ubuntu under WSL2.

The audit was limited to the commands and evidence collected during the exercise. It did not include a complete Windows host investigation, external network penetration test, malware analysis, or comprehensive firewall assessment.

The process owner of the DNS listener on 10.255.255.254 was not established from the collected evidence.

A successful DNS response does not prove DNS security. Similarly, the absence of failed systemd units does not prove that the entire system is free from security issues.

## 13. Conclusion

The Day 22 investigation provided practical experience in Linux process monitoring, service management, socket inspection, and network interface analysis.

The audit identified expected system services, examined DNS listeners, checked failed services, and documented the WSL2 networking environment.

No confirmed compromise was identified from the evidence collected.

The exercise reinforced the importance of investigating unexpected processes and listening sockets before drawing security conclusions.

## 14. Learning Outcomes

Through this investigation, I practised:

- Identifying processes using `ps aux`.
- Understanding PID and PPID.
- Differentiating processes from services.
- Inspecting services with systemctl.
- Identifying listening sockets using `ss`.
- Understanding loopback and network interface addresses.
- Interpreting DNS test results.
- Documenting security observations and investigation limitations.

---

**Project:** 60 Days of Cybersecurity Learning
**Day:** 22
**Topic:** Linux Process and Service Security Audit
**Environment:** Ubuntu WSL2
**Author:** Cybersecurity Learning Portfolio
