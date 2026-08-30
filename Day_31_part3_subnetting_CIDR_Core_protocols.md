Day 31: Networking Core - Part 3: Subnetting, CIDR Notation & Core Protocols

---

## 1. What is Subnetting?

### Definition
**Subnetting** is the process of logically dividing a single large physical or virtual IP network into two or more smaller, isolated sub-networks (known as **Subnets**).

### Why do we need Subnetting?
1. **Network Performance:** Reduces network broadcast traffic and reduces congestion.
2. **Security Isolation:** Allows separation of sensitive resources (e.g., databases) from public access.

# Subnet Division Flowchart

```text
[ Global Network Range: 10.0.0.0/16 (65,536 IPs) ]
                       |
       +---------------+---------------+
       |                               |
       v                               v
[ Public Subnet ]              [ Private Subnet ]
CIDR: 10.0.1.0/24              CIDR: 10.0.2.0/24
(256 Total IPs)                (256 Total IPs)
       |                               |
       +---> Web Servers               +---> Application Backend
       +---> Load Balancers            +---> Managed Databases
```
---

## 2. CIDR Notation (Classless Inter-Domain Routing)

### Definition
**CIDR (Classless Inter-Domain Routing)** is a method of allocating IP addresses that replaces the old Class-based system (Class A, B, C). It represents an IP address along with its associated network mask using a prefix length notation (e.g., `/24`).

### Understanding the `/` Prefix Length
An IPv4 address consists of **32 bits**. The CIDR prefix indicates how many bits are dedicated to the **Network ID**, leaving the remaining bits for the **Host ID**.

# IPv4 Bit Structure (32 Bits Total)
```
+------------------------------------+------------------------+
|   Network ID Bits (Prefix Length)   |     Host ID Bits       |
+------------------------------------+------------------------+
```
### Mathematical Formula for IP Calculation
- **Total Available IPs:** `2^(32 - CIDR Prefix)`
- **Usable Host IPs:** `2^(32 - CIDR Prefix) - 2`

> **Rule:** Every subnet reserves **2 IP addresses**:
> 1. **Network Address (First IP):** Represents the subnet itself.
> 2. **Broadcast Address (Last IP):** Used to transmit messages to all hosts on the subnet.

---

## 3. CIDR Lookup Reference Table

| CIDR Prefix | Network Bits | Host Bits | Formula | Total IPs | Usable IPs | Common Cloud Use Case |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **/32** | 32 | 0 | 2^0 | 1 | 1 | Single host / Specific Bastion Server |
| **/28** | 28 | 4 | 2^4 | 16 | 14 | Small database cluster subnet |
| **/24** | 24 | 8 | 2^8 | 256 | 254 | Standard Public / Private Cloud Subnet |
| **/16** | 16 | 16 | 2^16 | 65,536 | 65,534 | Standard VPC / Network Address Space |
| **/8** | 8 | 24 | 2^24 | 16,777,216 | 16,777,214 | Enterprise ISP Global Routing |

---

## 4. Visual Breakdown: How IP Digits Change in a Subnet

When inspecting a `/24` subnet range (e.g., `10.0.1.0/24`), the first 3 octets (`10.0.1.`) remain fixed. Only the **last octet (4th digit)** increments from `0` to `255`.

# Subnet Range Mapping (10.0.1.0/24)
```
Index   IP Address      Role / Status
------------------------------------------------------------
1       10.0.1.0        Network Address (Reserved)
2       10.0.1.1        First Usable Host IP
3       10.0.1.2        Host IP
...     ...             ...
129     10.0.1.128      Host IP (Halfway mark)
...     ...             ...
255     10.0.1.254      Last Usable Host IP
256     10.0.1.255      Broadcast Address (Reserved)
```
---

## 5. Ports and Protocols

### What is a Port?
A **Port Number** is a 16-bit logical channel number (`0` to `65535`) assigned to network processes to direct incoming data traffic to the correct application running on a server.

# Port Routing Visual Map
```
Incoming Request ---> [ Server IP: 192.168.1.50 ]
                                 |
              +------------------+------------------+
              |                                     |
              v                                     v
       [ Port 80 ]                           [ Port 22 ]
   (Web Server / Nginx)                    (SSH Terminal)
```
### Core DevOps Protocols Reference Table

| Protocol | Full Form | Default Port | Transport Protocol | Primary Purpose & Security Status |
| :--- | :--- | :--- | :--- | :--- |
| **HTTP** | Hypertext Transfer Protocol | 80 | TCP | Unencrypted web traffic (Unsecured). |
| **HTTPS** | Hypertext Transfer Protocol Secure | 443 | TCP | SSL/TLS encrypted web traffic (Secured). |
| **SSH** | Secure Shell | 22 | TCP | Secure remote terminal access to Linux servers. |
| **DNS** | Domain Name System | 53 | UDP / TCP | Translates domain names to IP addresses. |
| **DHCP** | Dynamic Host Configuration Protocol | 67 (Server) / 68 (Client) | UDP | Automatically assigns IP addresses to devices. |

---

## 6. Frequently Asked Interview Questions

### Q1: How many usable IP addresses are available in a `/24` subnet on AWS?
* **Answer:** Normally, a `/24` subnet has `256 - 2 = 254` usable IPs. However, **AWS reserves 5 IP addresses** per subnet (Network Address, VPC Router, DNS Server, Future Use, and Broadcast Address). Therefore, on AWS, a `/24` subnet has **251 usable IPs**.

### Q2: What is the difference between Port 80 and Port 443?
* **Answer:** Port 80 is used by HTTP to send plain-text data, making it vulnerable to interception. Port 443 is used by HTTPS, encrypting all communication via SSL/TLS certificates before transmission.

### Q3: If a server cannot be accessed via SSH, what is the first networking check to perform?
* **Answer:** Check if **Port 22** is blocked by server firewalls (`UFW`, `iptables`) or Cloud Security Groups, and verify if the client's IP is authorized to reach that port.

---

## 7. Multiple-Choice Questions (MCQs)

#### Q1. How many total IP addresses are provided by a `/28` CIDR block?
- A) 8
- B) 16
- C) 32
- D) 64
* **Correct Answer:** **B) 16** (`2^(32-28) = 2^4 = 16`)

#### Q2. Which default port does a secure Linux remote login connection use?
- A) Port 80
- B) Port 443
- C) Port 22
- D) Port 53
* **Correct Answer:** **C) Port 22**

#### Q3. Why are 2 IP addresses subtracted when calculating usable host IPs in a standard subnet?
- A) Reserved for Router and DNS
- B) Reserved for Network Address and Broadcast Address
- C) Reserved for Public IP and Private IP
- D) Reserved for Gateway and DHCP Server
* **Correct Answer:** **B) Reserved for Network Address and Broadcast Address**

#### Q4. Which protocol translates a domain name (e.g., `google.com`) into an IP address?
- A) DHCP
- B) HTTP
- C) SSH
- D) DNS
* **Correct Answer:** **D) DNS**
