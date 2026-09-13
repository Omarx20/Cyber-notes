https://nmap.org/nsedoc/
# 1.Nmap
---
##
namp is powerful for : (manuplating packets , Ready-made scripts) -->firewalls evaision 
---
# 2.SCAN-TYPES
----
##
1. -sT : making a complete tcp 3 way hand shake --> if you don't have the root premmison
2. -sS : sends only syn packet then RST --> faster 
3. -sU : sends UDB packet
4. -sN : tcp packet without any flags
6. -sF : tcp packet with FIN flag 
7. -sX : sends a packet with 3 flags (fin,psh,urg)
8. -O : specefies the os system
9. -sV : specifies the serviece on ports
10. -v : gives more informations
11. -oA : saving output in any format
12. -oN : normal format
13. -oG : grepable format
14. -A : like -sV + -O +
15. -p : to specify ports to scan
16. -T : for aggresive --> less time --> less
17. -sn : sending icmp and tcp packets for hosts
18. -Pn : doesn't check if host is exist by not sending ICMP echo request as usual
19. -f : Used to fragment the packets (i.e. split them into smaller pieces) making it less likely that the packets will be detected by a firewall or IDS.
20. --mtu <number>, accepts a maximum transmission unit size to use for the packets sent. This must be a multiple of 8.
21. --scan-delay <time>ms:- used to add a delay between packets sent. This is very useful if the network is unstable, but also for evading any time-based firewall/IDS triggers which may be in place.
22. --badsum:- this is used to generate in invalid checksum for packets. Any real TCP/IP stack would drop this packet, however, firewalls may potentially respond automatically, without bothering to check the checksum of the packet. As such, this switch can be used to determine the presence of a firewall/IDS
---
# 3.SCRIPTS  
---
##
 /usr/share/nmap/scripts/script.db
--script=<script_name> --script-args <args>
1. safe:- Won't affect the target
2. intrusive:- Not safe: likely to affect the target
3. vuln:- Scan for vulnerabilities
4. exploit:- Attempt to exploit a vulnerability
5. auth:- Attempt to bypass authentication for running services (e.g. Log into an FTP server anonymously)
6. brute:- Attempt to bruteforce credentials for running services
7. discovery:- Attempt to query running services for further information about the network (e.g. query an SNMP server).
8. 
