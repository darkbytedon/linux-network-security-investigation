# Day 12: Linux Network and Security Investigation

## 1. Objective
Investigate Linux network configuration, DNS resolution, listening services, active connections, processes and firewall configuration.

## 2. Environment
- Operating system: Ubuntu on WSL2
- Linux user: dondon
- Hostname: dondada
- Network interface: eth0
- IPv4 address: 172.18.49.100

## 3. Network Configuration
Commands used:
- ip addr
- ip route
- hostname
- hostname -I
- cat /etc/resolv.conf

Findings:
- Network interface eth0 was UP.
- Ubuntu IPv4 address: 172.18.49.100/20.
- Default gateway: 172.18.48.1.
- DNS resolver: 10.255.255.254.

## 4. Connectivity and DNS Investigation
Commands used:
- ping -c 20 1.1.1.1
- nslookup google.com
- ping -c 10 172.18.48.1
- ip neigh show 172.18.48.1

Findings:
- External IP connectivity succeeded with 5% packet loss in a 20-packet test.
- DNS resolution successfully returned IPv4 and IPv6 addresses for google.com.
- The gateway did not respond to ICMP ping.
- The gateway MAC address was discovered and its neighbor state was REACHABLE.

## 5. Listening Services and Processes
Commands used:
- ss -tuln
- sudo ss -tulpn
- ps -p 82 -o pid,comm,args
- ps -p 206 -o pid,comm,args

Findings:
- systemd-resolved was identified as the DNS resolver process (PID 82).
- chronyd was identified as the time synchronisation process (PID 206).
- DNS listeners were observed on port 53.
- chronyd used UDP port 323 on local loopback addresses.

## 6. Active Network Connections
Commands used:
- ss -tun
- ss -tan

Findings:
- A UDP socket was observed with remote port 123, consistent with time synchronisation traffic.
- TCP DNS listeners were observed on port 53.
- A TCP connection to remote port 80 was in TIME-WAIT state.

## 7. Firewall Investigation
Commands used:
- ufw --version
- sudo ufw status verbose
- sudo ufw status numbered
- sudo ufw show added
- sudo ufw show raw

Findings:
- UFW version 0.36.2 was installed.
- UFW status was inactive.
- No user-added UFW rules were configured.
- Raw IPv4 and IPv6 chain policies displayed ACCEPT.

## 8. Security Lessons
- DNS translates domain names into IP addresses.
- Listening ports should be associated with their owning processes.
- Network services should only listen on interfaces necessary for their purpose.
- TCP and UDP have different connection behaviours.
- TIME-WAIT is a normal TCP connection state.
- Firewall installation does not automatically mean firewall protection is active.
- WSL2 networking has environment-specific behaviour that must be considered during investigations.

## 9. Conclusion
The investigation demonstrated how to inspect Linux network interfaces, routing, DNS, listening services, active connections, processes and firewall configuration.

## 10. Limitations
This investigation was conducted in a local Ubuntu WSL2 training environment. Findings should not be assumed to represent a production Linux server or the complete firewall configuration of the Windows host.
