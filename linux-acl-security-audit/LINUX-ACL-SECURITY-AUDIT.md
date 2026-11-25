# Linux Access Control List (ACL) Security Audit

## 1. Objective

The objective of this investigation was to understand and assess Linux Access Control Lists (ACLs), identify unnecessary user permissions, test effective access, and apply appropriate remediation.

## 2. Environment

- Operating system: Ubuntu on WSL2
- Test user: dondon
- Additional test account: alice
- Tools: getfacl, setfacl, find, chmod, namei, sudo

## 3. Investigation Activities

The following activities were performed:

1. Inspected permissions on `/etc/shadow`, `/etc/sudoers`, and the user's home directory.
2. Used `getfacl` to inspect file and directory ACLs.
3. Created an isolated ACL testing environment.
4. Granted Alice read-only access to a test report.
5. Tested read and write access using `sudo -u alice`.
6. Investigated directory traversal restrictions.
7. Examined the ACL mask and effective permissions.
8. Removed unnecessary named-user ACL entries.
9. Performed a recursive ACL audit under `/home`.

## 4. Security Finding

An unnecessary named-user ACL entry was discovered:

`user:alice:r--`

The entry granted Alice read access to:

`/home/dondon/acl-lab/confidential-report.txt`

The file ACL permitted reading, but Alice could not initially reach the file because the home directory denied her traversal permission.

This demonstrated that file permissions and parent directory permissions must both be considered during access-control assessments.

## 5. Remediation

The unnecessary ACL entry was removed using:

`setfacl -x u:alice /home/dondon/acl-lab/confidential-report.txt`

The resulting ACL no longer contained Alice's named-user entry.

## 6. Verification

The file ACL was inspected again using:

`getfacl /home/dondon/acl-lab/confidential-report.txt`

A subsequent audit using `find` and `getfacl` returned no matching named-user or named-group ACL entries under `/home`.

## 7. Key Technical Lessons

- Linux permissions apply separately to files and directories.
- Directory execute permission controls traversal.
- Named-user ACLs can grant additional access beyond standard owner/group/other permissions.
- ACL masks restrict effective permissions.
- Removing a named ACL entry does not necessarily remove access granted through ordinary permissions.
- The plus sign in `ls -l` indicates an extended ACL, not automatically a vulnerability.
- Security remediation must be followed by verification.

## 8. Conclusion

This investigation demonstrated practical Linux access-control auditing, permission analysis, ACL configuration, access testing, remediation, and verification.

The exercises reinforced the importance of least privilege and examining the complete access-control path when assessing Linux systems.
