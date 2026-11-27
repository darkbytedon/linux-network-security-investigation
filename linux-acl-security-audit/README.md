# Linux Access Control List (ACL) Security Audit

## Project Overview

This project documents a practical Linux ACL security investigation conducted in Ubuntu under Windows Subsystem for Linux 2 (WSL2).

The investigation focused on identifying unnecessary user permissions, testing effective access, examining directory traversal restrictions, applying ACL remediation and verifying the resulting configuration.

## Audit Environment

| Item | Details |
|---|---|
| Operating System | Ubuntu |
| Environment | WSL2 |
| Primary User | dondon |
| Additional Test User | alice |
| Tools | getfacl, setfacl, find, chmod, namei, sudo |

## Investigation Objectives

- Inspect file and directory ACLs.
- Identify unnecessary named-user permissions.
- Test access using a separate user account.
- Examine directory traversal restrictions.
- Understand ACL masks and effective permissions.
- Remove unnecessary ACL entries.
- Verify remediation.

## Tools Used

| Tool | Purpose |
|---|---|
| getfacl | Inspect ACL entries |
| setfacl | Modify ACL entries |
| find | Search filesystem paths |
| chmod | Modify traditional permissions |
| namei | Inspect directory traversal permissions |
| sudo -u | Test access as another user |

## Investigation Activities

### 1. Sensitive File Inspection

Permissions and ACLs were inspected on:

- /etc/shadow
- /etc/sudoers
- The user's home directory

### 2. Controlled ACL Testing

An isolated ACL testing environment was created.

A named-user ACL entry granted Alice read permission on a test report.

Access was tested using sudo -u alice.

### 3. Directory Traversal Investigation

The investigation demonstrated that file permissions alone do not determine access.

A user also requires execute (traversal) permission on every parent directory in the filesystem path.

Although Alice's ACL entry granted read permission on the test file, the parent home-directory permissions initially prevented her from reaching it.

### 4. ACL Mask Investigation

The audit examined how ACL masks affect the effective permissions of named users and groups.

This demonstrated why ACL entries must be interpreted together with their associated masks.

## Security Finding: Unnecessary Named-User Permission

The investigation identified this ACL entry:

user:alice:r--

Associated file:

/home/dondon/acl-lab/confidential-report.txt

The entry granted Alice read permission on the file. However, parent directory permissions initially restricted her ability to reach it.

The investigation identified an unnecessary permission configuration. It did not establish that Alice successfully accessed the confidential file before remediation.

## Remediation

The unnecessary named-user ACL entry was removed using:

setfacl -x u:alice /home/dondon/acl-lab/confidential-report.txt

## Verification

The resulting ACL was inspected using getfacl.

The resulting output contained no named-user entry for Alice.

A subsequent audit using find and getfacl returned no matching named-user or named-group ACL entries under /home.

This demonstrated the importance of verifying access-control changes after remediation.

## Security Recommendations

1. Apply least privilege when assigning ACL permissions.
2. Review named-user and named-group ACL entries periodically.
3. Inspect parent directory permissions when investigating access failures.
4. Examine ACL masks when determining effective access.
5. Remove unnecessary permissions through controlled changes.
6. Verify access-control changes after remediation.
7. Include recursive ACL reviews in appropriate Linux security audits.

## Skills Demonstrated

- Linux access-control auditing.
- File and directory permission analysis.
- ACL inspection and modification.
- Effective permission analysis.
- Directory traversal investigation.
- Controlled access testing.
- Security remediation and verification.
- Technical security documentation.

## Related Projects

- Day 21: Linux Privilege and Permissions Security Audit
- Day 22: Linux Process and Service Security Audit

## Full Technical Report

See the complete investigation:

LINUX-ACL-SECURITY-AUDIT.md

## Limitations

This was an educational security investigation conducted in Ubuntu WSL2.

It was not a complete penetration test or an assessment of a third-party system.

The findings represent observations from the documented local lab.

## Disclaimer

This project was conducted for educational and professional development purposes in a controlled local environment.

---

Author: Cybersecurity Learning Portfolio
Project: Linux ACL Security Audit
Environment: Ubuntu WSL2
