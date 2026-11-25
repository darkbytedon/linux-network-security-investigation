# Day 21: Linux Privilege and Permissions Security Audit

## 1. Executive Summary

This practical security audit examined Linux file permissions, Access Control Lists (ACLs), special permission bits, writable directories, sudo configuration and Linux capabilities in an Ubuntu WSL2 environment.

The objective was to identify potentially insecure privilege configurations, understand their security implications and evaluate whether observed configurations followed the principle of least privilege.

The investigation identified one capability assignment on `/usr/bin/ping`, several expected system permission configurations and no world-writable directories in the specifically searched `/etc` and `/usr/local` trees.

## 2. Audit Environment

- Operating system: Ubuntu on WSL2
- User account: dondon
- Shell: Bash
- Tools: find, ls, stat, getfacl, getcap, getpcaps, capsh, dpkg, sudo and Git

## 3. File Permission Investigation

The following system files were inspected:

| File | Observed permissions | Assessment |
|---|---|---|
| `/etc/passwd` | 644 | Readable account information |
| `/etc/shadow` | 640 | Restricted password database |
| `/etc/sudoers` | 440 | Restricted administrative configuration |

The investigation confirmed that ordinary users could read account information but could not modify these protected system files.

The `/etc/sudoers` configuration was inspected without modifying it. Changes to this file should be performed using `visudo`.

## 4. ACL Security Investigation

The audit used:

- `getfacl` to inspect ACL entries.
- `setfacl` to modify ACLs in controlled lab exercises.
- `chmod` to adjust traditional permissions.

The exercises demonstrated that ACL masks can restrict effective permissions and that directory traversal permissions affect access to files.

A separate ACL security audit was documented and published previously.

### Additional observation

`/usr/bin/ping` displayed a trailing `+` in its `ls -l` listing. However, `getfacl` showed only the basic owner, group and other entries.

This discrepancy was not resolved conclusively and is recorded as an observation requiring further investigation, not a confirmed vulnerability.

## 5. SUID, SGID and Sticky Bit Investigation

The audit identified special permission configurations using:

```bash
sudo find /usr/bin -type f -perm -4000 -ls
sudo find /usr/bin -type f -perm -2000 -ls
```

Examples included `/usr/bin/passwd`, `/usr/bin/su`, `/usr/bin/mount`, `/usr/bin/umount` and other system utilities.

The investigation also inspected:

```bash
ls -ld /tmp /var/tmp /usr/local/share/fonts
```

Findings:

- `/tmp` uses mode 1777, allowing shared temporary-file creation while the sticky bit restricts deletion or renaming of other users' entries.
- `/var/tmp` also uses mode 1777.
- `/usr/local/share/fonts` uses mode 2775, applying SGID group inheritance to new entries.

The presence of SUID or SGID is not automatically a vulnerability. Each executable must be assessed for ownership, package integrity, necessity and risk.

## 6. Writable File and Directory Investigation

The following files were inspected:

```bash
ls -l /etc/passwd /etc/shadow /etc/sudoers
```

The search for world-writable files under `/etc` returned no matches:

```bash
sudo find /etc -xdev -type f -perm -0002 -ls
```

The following directories were inspected:

```bash
ls -ld /tmp /var/tmp /var/log /etc /usr/local/bin
```

Observed configurations:

| Directory | Permissions | Interpretation |
|---|---|---|
| `/etc` | 755 | Root-controlled configuration directory |
| `/tmp` | 1777 | Shared temporary directory with sticky bit |
| `/var/tmp` | 1777 | Shared temporary directory with sticky bit |
| `/usr/local/bin` | 755 | Root-controlled executable directory |
| `/var/log` | 775 | Root and syslog group can write |

The group lookup showed:

```text
syslog:x:101:
```

No supplementary group members were listed.

The search for world-writable directories under `/etc` and `/usr/local` returned no matches:

```bash
sudo find /etc /usr/local -xdev -type d -perm -0002 -ls
```

These results apply only to the directories searched and do not establish that the entire system is secure.

## 7. Linux Capabilities Investigation

Linux capabilities divide traditional root privileges into more specific permissions.

The following tools were used:

- `getcap`: identifies file capabilities.
- `capsh`: displays process capability information.
- `getpcaps`: inspects capabilities held by processes.

The executable search was:

```bash
sudo getcap -r /usr/bin /usr/sbin /usr/local/bin 2>/dev/null
```

The observed result was:

```text
/usr/bin/ping cap_net_raw=ep
```

The `CAP_NET_RAW` capability allows the program to use raw and packet sockets for network operations.

This is a common and legitimate configuration for ping.

The executable was investigated further:

```bash
ls -l /usr/bin/ping
dpkg -S /usr/bin/ping
getcap /usr/bin/ping
getfacl -e /usr/bin/ping
sudo dpkg -V iputils-ping
```

Package verification returned no output, meaning no discrepancies were reported by the verification performed.

Additional searches under `/bin` and `/sbin` found no additional capability assignments.

## 8. Process Capability Investigation

The current user's shell was inspected using:

```bash
capsh --print
```

The ordinary user showed:

```text
Current: =
Ambient set =
```

The root context showed:

```text
Current: =ep
Ambient set =
```

The investigation also used:

```bash
echo $$
getpcaps 99
```

The inspected ordinary shell process had no effective or permitted capabilities listed.

The bounding set was also examined. Its presence does not mean every listed capability is currently active.

## 9. Sudo Privilege Investigation

The user's sudo permissions were inspected using:

```bash
sudo -l
id
sudo id
```

The account had administrative sudo privileges through the sudo group.

The audit also investigated the installed sudo implementation and package ownership.

The presence of sudo access is expected for an administrator account, but unrestricted administrative privileges should be granted only to trusted users who require them.

## 10. Security Recommendations

1. Follow the principle of least privilege when assigning user and group permissions.
2. Regularly review SUID and SGID executables.
3. Investigate unnecessary or excessive Linux capabilities.
4. Restrict write access to system executable directories.
5. Protect sensitive configuration files such as `/etc/shadow` and `/etc/sudoers`.
6. Monitor changes to system logs and security-sensitive files.
7. Review sudo group membership regularly.
8. Investigate unexplained ACL indicators and filesystem metadata discrepancies.
9. Repeat permission audits after major system updates.
10. Expand searches beyond individual directories when conducting a full-system audit.

## 11. Limitations

This was a targeted educational security audit conducted in Ubuntu WSL2.

It did not constitute a complete penetration test or comprehensive assessment of every file, process, service or kernel configuration.

The unexplained `+` indicator on `/usr/bin/ping` remains unresolved.

No exploitation or unauthorized privilege escalation was performed.

## 12. Conclusion

The practical investigation developed hands-on skills in Linux permission analysis, ACL management, special permission auditing, writable-directory discovery, sudo review and Linux capability inspection.

The most important lesson was that the existence of a special permission or capability does not automatically indicate a vulnerability. Security assessment requires understanding the purpose, ownership, scope and necessity of each privilege.

The exercises reinforced the principle of least privilege and the importance of evidence-based security reporting.
