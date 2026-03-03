# Information Security

## Cisco Packet Tracer Practicals

### ACL Generation Rules

- SLIP : ACL That blocks HTTP and HTTPS Access 
```
ipv6 access-list achilles
deny tcp any host <ipv6 address of server> eq www
deny tcp any host <ipv6 address of server> eq 443
permit ipv6 any any
exit

interface <interface name>
ipv6 traffic-filter achilles in
exit

```
- SLIP : ACT That block ICMP Traffic
```
ipv6 access-list achilles
deny icmp any host <ipv6 address of server>
permit any any
exit

interface <interface name>
ipv6 traffic-filter achilles in
exit

```
- SLIP : ACLthe will permit FTP and HTTP access on R1
- PC1 FTP 
```
access-list achilles
permit tcp host <PC1 IP> host <Server IP> eq ftp
interface <interface Name>
ip traffic-filter achilles in 
!!!OR USE THIS!!! 
ip access-group achilles in

```
- PC2 HTTP
```
access-list achilles
permit tcp host <PC2 IP> host <Server IP> eq www
interface <interface name>
ip traffic-filter achilles in 
!!!OR USE THIS!!! 
ip access-group achilles in

```

SLIP : permit one LAN will permit one LAN to remotely access a device in another LAN using SSH Protocol 


```

access-list 110 permit tcp 10.101.117.48 0.0.0.3 10.101.117.32 0.0.0.3 eq 22
access-list 110 permit icmp any any
access-list 110 deny ip any any

interface g0/1
 ip access-group 110 in
exit

```
---

### SLIP 

Create the following topology and

Configure OSPF MD5 authentication

Configure NTP and configure routers to log messages to the Syslog Server

- Create Topology
- Add HWIC-2T to routers
- Configure IPv4
- Configure OSPF
R0
```
enable 
configure terminal
router ospf 1
network 192.168.1.0 0.0.0.255 area 1
network 10.1.1.0 0.0.0.3 area 1
exit
```
R1
```
enable 
configure terminal
router ospf 1
network 10.1.1.0 0.0.0.3 area 1
network 10.2.2.0 0.0.0.3 area 1
exit
```
R2
```
enable 
configure terminal
router ospf 1
network 10.2.2.0 0.0.0.3 area 1
network 192.168.3.0 0.0.0.255 area 1
exit
```
- Ping the server to check 
- Configure MD5 Authentication

R0
```
enable 
configure terminal 
interface Serial 0/1/0
ip ospf authentication message-digest
ip ospf message-digest-key 1 md5 cisco123
exit

```
R1
```
enable 
configure terminal
interface Serial 0/1/0
ip ospf authentication message-digest
ip ospf message-digest-key cisco123
exit
```

- Check MD5 Authentication
`show ip ospf interface Serial 0/1/0`

- Configure NTP on R0
- Enable NTP Service on Server
    - Service -> NTP -> ON
```
enable 
configure terminal
ntp server <Server IP>
ntp update-calendar
exit
show clock
```

- Configure SYSLOG Server
- Enable SYSLOG service on Server
    - Services -> SYSLOG -> ON
```
enable
configure terminal
logging <Server IP>
exit
```
- Ping server from pc to check if it is logging

11. Configure SSH

R0
```
enable
configure terminal
ip domain-name sarang.com
hostname r0
cypto key generate rsa
512
line vty 0 4
transport input ssh
login local
exit
username sarang privilege 15 password 1234

```
check ssh from pc connect to router 
<br/>
`ssh <R0 IP>` <br/>
`<password>`

---

### SLIP 

Create the following topology and

Configure OSPF MD5 authentication

Configure a local user account on R1 and configure authenticate on the console and vty lines using local AAA

Verify local AAA authentication from the R1 console and the PC0 client and PC1 Client

- Create Topology
- Configure IP Addresses
- Configure OSPF on both interface

R1
```
enable
configure terminal
router ospf 1
network 192.168.1.0 0.0.0.255 area 1
network 192.168.2.0 0.0.0.255 area 1
exit

```
Verify OSPF Configuration (Ping Server From Pc)

- Configure MD5 Authentication

R1
```
enable 
configure terminal
interface GigabitEthernet0/0
ip ospf authentication message-digest
ip ospf message-digest-key 1 md5 cisco123
exit
interface GigabitEthernet0/1
ip ospf authentication message-digest
ip ospf message-digest-key 1 md5 cisco123
exit

```

Verify MD5 by 
```
show ip ospf interface GigabitEthernet0/0
```
- Configure AAA Authentication
On TACACS server 
-> services -> AAA -> on -> Enter details
On RADIUS server
-> services -> AAA -> on -> Enter details

R1
```
enable 
configure terminal
aaa new-model
tacacs-server host <tacacs server ip> key cisco
radius-server host <radius server ip> key cisco
aaa authentication login achilles group tacacs+ group radius local
line vty 0 4
authentication login achilles
exit
```
Verify from PC0 if able to use telnet service
PC0 - Command prompt
`telnet <router-ip> `

---

### SLIP 


Create the following topology and

Configure an ACL that will permit one LAN will permit one LAN to remotely access a device in another LAN using SSH Protocol

Besides ICMP all traffic from other network is denied

Verify the ACL implementation

- Create Topology
- Configure IPs in router and pc
- Configure VLAN in each Switch
```
enable
configure terminal
interface vlan 1
ip address <given ip> 255.255.255.0
no shutdown
exit
ip default-gateway <gateway ip>

```

- Configure SSH Protocol
R0
```
enable
configure terminal
ip domain-name sarang.com
hostname r0
crypto key generate rsa
512
line vty 0 4
transport input ssh
login local
exit
username sarang privilege 15 password 1234

```
- Check SSH From PC
```
ssh -l sarang <router ip>
```

- Configure ACL

```
access-list 110 permit tcp 10.101.117.48 0.0.0.3 10.101.117.32 0.0.0.3 eq 22
access-list 110 permit icmp any any
access-list 110 deny ip any any

interface g0/1
 ip access-group 110 in
exit

```
- Verify
```
show access-lists
show ip interface brief
ping 10.101.117.35
ssh -l achilles 10.101.117.35

```

---


### SLIP 

Create the following topology using static routing and

Configure, apply and verify an ACL that will block HTTP access on R3

Configure, apply and verify an ACL that will block HTTPS access on R3

Configure ACL that block ICMP 

1. Create Topology
2. Add HWIC-2T to routers
3. Configure IPv6 (Normally to server and PC)

For Router: 
```
enable
configure terminal
ipv6 unicast-routing
interface GigabitEthernet0/0
ipv6 address <address>
ipv6 rip a enable
no shutdown
exit

```
4. Ping The server to check connection
5. Configure ACLs on router
R1
```
ipv6 access-list sarang
deny tcp any host <IPv6 address of server> eq <service>
deny tcp any host <IPv6 address of server> eq <port>
permit ipv6 any any
exit

interface <interface name>
ipv6 traffic-filter sarang in
exit

```

- Configure ACL to Block ICMP Traffic
```
ipv6 access-list nikhil
deny icmp any host <IPv6 address of server>
permit ipv6 any any
exit

interface <interface name>
ipv6 traffic-filter sarang in
exit

```
---