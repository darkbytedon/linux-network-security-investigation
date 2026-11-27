# Linux Privilege and Permissions Security Audit

## Project Overview

This project documents a practical Linux privilege and permissions security audit conducted in Ubuntu running under Windows Subsystem for Linux 2 (WSL2).

The investigation examined file permissions, Access Control Lists (ACLs), SUID and SGID executables, sticky bits, writable directories, Linux capabilities and sudo privileges.

The primary objective was to understand how Linux controls access to sensitive resources and to identify configurations that may introduce unnecessary privilege or security risks.

## Audit Environment

| Item | Details |
|---|---|
| Operating System | Ubuntu |
| Environment | Windows Subsystem for Linux 2 (WSL2) |
| User Account | dondon |
| Shell | Bash |
| Audit Type | Linux Privilege and Permissions Security Audit |
| Project | 60 Days of Cybersecurity Learning |
| Day | 21 |

## Investigation Objectives

- Examine permissions on sensitive Linux system files.
- Inspect file and directory ACLs.
- Identify SUID and SGID executables.
- Understand sticky-bit directory permissions.
- Search selected system directories for world-writable files and directories.
- Review Linux file capabilities.
- Inspect process capabilities.
- Examine sudo privileges and administrative access.
- Document security observations and recommendations.

## Tools and Commands Used

| Tool | Purpose |
|---|---|
| `ls -l` | Inspect file permissions and ownership |
| `stat` | Examine file metadata |
| `chmod` | Modify traditional permissions in controlled exercises |
| `getfacl` | Inspect access control lists |
| `setfacl` | Modify ACLs in controlled exercises |
| `find` | Search for special permissions and writable files |
| `getcap` | Identify file capabilities |
| `getpcaps` | Inspect process capabilities |
| `capsh` | Examine capability sets |
| `sudo -l` | Review sudo privileges |
| `dpkg -S` | Identify package ownership |
| `dpkg -V` | Verify installed package files |

## Key Findings

### 1. Sensitive File Permissions

The investigation recorded these permissions:

| File | Permissions | Security interpretation |
|---|---|---|
| `/etc/passwd` | 644 | Account information is readable |
| `/etc/shadow` | 640 | Password database access is restricted |
| `/etc/sudoers` | 440 | Administrative configuration is restricted |

The sudoers file was inspected without directly modifying it. Administrative changes should be made using `visudo`.

### 2. ACL Investigation

The audit used `getfacl` and `setfacl` to examine and practise Linux ACL management.

The exercises demonstrated how ACL entries, ACL masks and directory traversal permissions affect effective access.

An unexplained `+` indicator appeared in the permission listing for `/usr/bin/ping`. The available ACL output did not resolve this discrepancy conclusively, so it remains an observation requiring further investigation.

### 3. Special Permission Bits

The investigation examined SUID and SGID executables.

Examples identified included:

- `/usr/bin/passwd`
- `/usr/bin/su`
- `/usr/bin/mount`
- `/usr/bin/umount`

The audit also inspected shared directories:

- `/tmp` — mode 1777
- `/var/tmp` — mode 1777
- `/usr/local/share/fonts` — mode 2775

These configurations demonstrate the use of sticky-bit and SGID permissions.

The presence of SUID or SGID does not automatically establish a vulnerability. Ownership, necessity, executable behaviour and package integrity must also be considered.

### 4. Writable Directory Investigation

Targeted searches under `/etc` and `/usr/local` did not return world-writable directories.

The investigation also recorded:

- `/etc` — mode 755
- `/tmp` — mode 1777
- `/var/tmp` — mode 1777
- `/usr/local/bin` — mode 755
- `/var/log` — mode 775

These observations are limited to the inspected paths and do not establish the security of every directory on the system.

### 5. Linux Capabilities Investigation

The file capability search identified:

`/usr/bin/ping cap_net_raw=ep`

CAP_NET_RAW permits operations involving raw and packet sockets.

This is a common capability assignment for ping and is not automatically a security vulnerability.

The investigation also examined package ownership, file metadata, ACL information and package verification.

No discrepancies were reported by the documented `dpkg -V iputils-ping` check.

### 6. Process Capabilities

The audit examined capability information for ordinary-user and root contexts using `capsh --print` and `getpcaps`.

The inspected ordinary-user shell had no effective or permitted capabilities listed.

The investigation also distinguished the capability bounding set from capabilities actively held by a process.

### 7. Sudo Privileges

The account's administrative privileges were examined using:

- `sudo -l`
- `id`
- `sudo id`

The account had administrative sudo access through the sudo group.

This reinforces the importance of controlling administrative group membership and following the principle of least privilege.

## Security Recommendations

1. Apply least privilege when assigning file and directory permissions.
2. Review SUID and SGID executables periodically.
3. Investigate unnecessary or excessive Linux capabilities.
4. Prevent unauthorized write access to system executable directories.
5. Protect sensitive files such as `/etc/shadow` and `/etc/sudoers`.
6. Review sudo group membership regularly.
7. Investigate unexplained permission and ACL indicators.
8. Monitor changes to security-sensitive system files.
9. Repeat permission audits after significant system changes.
10. Expand audit scope when conducting a comprehensive security assessment.

## Limitations

This was a targeted educational security audit conducted in Ubuntu WSL2.

It did not constitute a complete penetration test or a comprehensive assessment of every file, directory, process or kernel configuration.

The unexplained permission indicator on `/usr/bin/ping` remains unresolved.

No exploitation or unauthorized privilege escalation was performed.

## Skills Demonstrated

- Linux file permission analysis.
- ACL inspection and management.
- SUID, SGID and sticky-bit analysis.
- Writable file and directory discovery.
- Linux capabilities investigation.
- Process capability inspection.
- Sudo privilege review.
- Security risk interpretation.
- Technical security documentation.

## Related Projects

- [Day 22: Linux Process and Service Security Audit](../linux-process-service-audit/README.md)
- [Linux ACL Security Audit](../linux-acl-security-audit/LINUX-ACL-SECURITY-AUDIT.md)

## Full Technical Report

See the complete investigation:

[DAY21-LINUX-PRIVILEGE-SECURITY-AUDIT.md](DAY21-LINUX-PRIVILEGE-SECURITY-AUDIT.md)

## Disclaimer

This project was conducted for educational and professional development purposes in a controlled local environment. The findings represent observations from the documented lab and are not a security certification or an assessment of a third-party system.

---

**Author:** Cybersecurity Learning Portfolio  
**Project:** 60 Days of Cybersecurity Learning  
**Topic:** Linux Privilege and Permissions Security Audit
