# Embedded Linux Networking 

## Overview

Networking in Embedded Linux enables communication between devices using protocols such as TCP/IP, UDP, Ethernet, Wi-Fi, and serial-based networking.

It is a core subsystem for IoT devices, routers, gateways, and industrial systems.

---

# 1. Network Stack Overview

```

Application Layer
↓
Transport Layer (TCP / UDP)
↓
Network Layer (IP)
↓
Data Link Layer (Ethernet / Wi-Fi)
↓
Physical Layer (PHY / Cable / Radio)

```id="stk01"

---

# 2. Linux Networking Architecture

```

User Space Applications
↓
Socket API
↓
Kernel Space (TCP/IP Stack)
↓
Network Drivers (Ethernet / Wi-Fi)
↓
Hardware (NIC / PHY)

```id="stk02"

---

# 3. Socket Programming Basics

## 3.1 What is a Socket?

A socket is an endpoint for communication.

Types:
- Stream Socket → TCP
- Datagram Socket → UDP

---

## 3.2 TCP Socket Flow

```

Server:
socket()
bind()
listen()
accept()
read()/write()

Client:
socket()
connect()
read()/write()

```id="tcp01"

---

## 3.3 UDP Socket Flow

```

socket()
sendto()
recvfrom()

```id="udp01"

---

# 4. TCP vs UDP

| Feature | TCP | UDP |
|--------|-----|-----|
| Connection | Connection-oriented | Connectionless |
| Reliability | High | Low |
| Speed | Slower | Faster |
| Ordering | Guaranteed | Not guaranteed |
| Use case | Web, SSH | Streaming, IoT |

---

# 5. IP Addressing

## IPv4 Example
```

192.168.1.10

```id="ip01"

## Types
- Public IP
- Private IP
- Loopback (127.0.0.1)

---

## Subnet Example

```

IP:      192.168.1.10
Mask:    255.255.255.0
Network: 192.168.1.0

```id="sub01"

---

# 6. Ports

Ports identify applications.

| Port | Service |
|------|--------|
| 22 | SSH |
| 80 | HTTP |
| 443 | HTTPS |

---

# 7. Network Interfaces in Linux

List interfaces:
```

ifconfig
ip addr

```id="iface01"

Common interfaces:
- eth0 (Ethernet)
- wlan0 (Wi-Fi)
- lo (loopback)

---

# 8. Routing

Routing decides packet path.

```

ip route

```id="route01"

Example:
```

default via 192.168.1.1 dev eth0

```id="route02"

---

# 9. DNS (Domain Name System)

Converts domain names → IP addresses.

Example:
```

google.com → 142.250.190.14

```id="dns01"

---

# 10. Network Configuration in Embedded Linux

## Static IP Setup

```

ip addr add 192.168.1.50/24 dev eth0
ip link set eth0 up
ip route add default via 192.168.1.1

```id="cfg01"

---

## DHCP Setup

Automatically assigns IP:
```

dhclient eth0

```id="dhcp01"

---

# 11. Embedded Networking Stack

```

Application
↓
Socket API
↓
Kernel TCP/IP Stack
↓
Driver (eth0 / wlan0)
↓
PHY / Hardware

```id="embstk01"

---

# 12. Ethernet Communication

## Frame Structure

```

| Destination MAC | Source MAC | Type | Payload | CRC |

```id="eth01"

---

## MAC Address Example
```

00:1A:2B:3C:4D:5E

```id="mac01"

---

# 13. Wi-Fi in Embedded Linux

Components:
- wpa_supplicant
- hostapd
- nl80211 driver

Connect to Wi-Fi:
```

wpa_supplicant -B -i wlan0 -c config.conf
dhclient wlan0

```id="wifi01"

---

# 14. Network Tools

## Ping
```

ping google.com

```id="ping01"

## Netstat
```

netstat -tuln

```id="net01"

## SS (modern replacement)
```

ss -tuln

```id="ss01"

## TCP Dump
```

tcpdump -i eth0

```id="tcpd01"

---

# 15. Embedded Networking Use Cases

- IoT devices
- Smart sensors
- Industrial controllers
- Routers and gateways
- Automotive ECUs

---

# 16. Common Networking Issues

- No IP assigned (DHCP failure)
- DNS not resolving
- Wrong gateway configuration
- Driver issues (eth0 down)
- Firewall blocking traffic

---

# 17. Debugging Steps

1. Check interface
```

ip link

```

2. Check IP
```

ip addr

```

3. Check routing
```

ip route

```

4. Test connectivity
```

ping 8.8.8.8

```

---

# 18. Summary

- Networking in Linux is based on TCP/IP stack
- Socket API connects user space to kernel
- Ethernet/Wi-Fi handled by drivers
- Embedded systems rely heavily on networking for communication

---


