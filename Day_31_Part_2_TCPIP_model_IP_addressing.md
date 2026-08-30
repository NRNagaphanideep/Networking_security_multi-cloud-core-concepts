Day 31: Networking Core - Part 2: TCP/IP Model & IP Addressing

---

## 1. The TCP/IP Model

### Definition
**TCP/IP** stands for **Transmission Control Protocol / Internet Protocol**. 
While the OSI model is a theoretical concept, the **TCP/IP Model** is the practical, real-world framework used by the global Internet and modern computer networks. It simplifies network communication into **4 functional layers**.

---

### OSI Model vs. TCP/IP Model Comparison
``` text
+------------------------------------+------------------------------------+
|        OSI MODEL (7 Layers)        |       TCP/IP MODEL (4 Layers)      |
+------------------------------------+------------------------------------+
| 7. Application Layer               |                                    |
| 6. Presentation Layer              | 1. Application Layer               |
| 5. Session Layer                   |                                    |
+------------------------------------+------------------------------------+
| 4. Transport Layer                 | 2. Transport Layer                 |
+------------------------------------+------------------------------------+
| 3. Network Layer                   | 3. Internet Layer                  |
+------------------------------------+------------------------------------+
| 2. Data Link Layer                 | 4. Network Access Layer            |
| 1. Physical Layer                  |    (Link Layer)                    |

+------------------------------------+------------------------------------+
```
---

### Layer-by-Layer Breakdown

#### 1. Application Layer
* **Role:** Combines OSI Layers 5, 6, and 7. Handles user-facing software protocols, data formatting, encryption, and session initialization.
* **Protocols:** HTTP, HTTPS, SSH, DNS, DHCP, FTP, SMTP.

#### 2. Transport Layer
* **Role:** Controls end-to-end communication, segmentation, error detection, and port multiplexing.
* **Protocols:**
  * **TCP (Transmission Control Protocol):** Connection-oriented, guarantees delivery.
  * **UDP (User Datagram Protocol):** Connectionless, lightweight, fast.

#### 3. Internet Layer
* **Role:** Equivalent to OSI Layer 3. Responsible for routing data packets across different networks using logical IP addresses.
* **Protocols:** IP (IPv4, IPv6), ICMP, ARP.

#### 4. Network Access Layer (Link Layer)
* **Role:** Combines OSI Layers 1 and 2. Responsible for placing raw bits onto physical media and handling physical hardware (MAC) addressing within local network segments.
* **Hardware/Protocols:** Ethernet, Wi-Fi, Switches, Network Interface Cards (NIC).

---

## 2. IP Addressing Fundamentals

### What is an IP Address?
An **IP (Internet Protocol) Address** is a unique numerical or alphanumerical identifier assigned to every device (computer, server, smartphone, container) connected to a network to enable communication across the Internet or local subnets.

---

## 3. IPv4 vs. IPv6
**IPv4:**  192.168.1.10  (32-bit, Decimal, Dot-separated)
**IPv6:**  2001:0db8:85a3:0000:0000:8a2e:0370:7334 (128-bit, Hexadecimal, Colon-separated)

### Detailed Comparison Table

| Feature | IPv4 (Internet Protocol v4) | IPv6 (Internet Protocol v6) |
| :--- | :--- | :--- |
| **Full Form** | Internet Protocol Version 4 | Internet Protocol Version 6 |
| **Address Size** | 32 bits (4 Bytes) | 128 bits (16 Bytes) |
| **Format** | Dotted Decimal (e.g., `172.217.14.206`) | Hexadecimal notation (e.g., `2001:db8::1`) |
| **Separation Symbol** | Dot (`.`) | Colon (`:`) |
| **Total Address Pool** | ~4.3 Billion ($2^{32}$) | ~340 Undecillion ($2^{128} \approx 3.4 \times 10^{38}$) |
| **Header Size** | Variable (20–60 bytes) | Fixed (40 bytes) |
| **Configuration** | Manual or via DHCP | Auto-configuration (SLAAC / DHCPv6) |
| **Cloud Pricing Impact** | Cloud providers charge for IPv4 due to exhaustion. | Usually free of charge on major cloud platforms. |

---

## 4. Public vs. Private IP Addresses

Every device in a network operates with two distinct types of IP addresses:

### Public vs. Private IP Architecture Diagram
``` text 
[ Local Network ]                                              [ Internet ]

+-------------------------+
| Mobile 1  (192.168.1.2) | --\
| Mobile 2  (192.168.1.3) |---> [ Wi-Fi Router ] ---- (Public IP: 49.207.12.5) ---> Google Server
| Laptop 1  (192.168.1.4) |--/   (Translates via NAT)
+-------------------------+
```
(Private IPs assigned by router)

### 1. Private IP Address
* **Definition:** Used exclusively within a local network (LAN) to identify devices communicating with each other internally.
* **Routability:** Non-routable on the public Internet.
* **Cost:** Free, reusable across different local networks worldwide.
* **Reserved Ranges (RFC 1918):**
  * `10.0.0.0` – `10.255.255.255` (Class A)
  * `172.16.0.0` – `172.31.255.255` (Class B)
  * `192.168.0.0` – `192.168.255.255` (Class C)

### 2. Public IP Address
* **Definition:** Assigned by an Internet Service Provider (ISP) or Cloud Provider to identify a device or network gateway on the global Internet.
* **Routability:** Globally routable across the public Internet.
* **Cost:** Paid/Restricted pool.
* **Mechanism:** Network Address Translation (**NAT**) maps multiple Private IPs behind a single Public IP address.

---

## 5. Frequently Asked Interview Questions

### Q1: Why do cloud providers charge extra for Public IPv4 addresses?
* **Answer:** IPv4 addresses are globally exhausted. To prevent hoarding and incentivize migration to IPv6 or private network topologies, cloud providers (like AWS) charge an hourly fee for every active or unattached Public IPv4 address.

### Q2: Can two different devices in two separate homes have the same Private IP address?
* **Answer:** Yes. Private IP addresses (such as `192.168.1.100`) are isolated within their respective local networks. As long as they do not overlap within the same local network, identical private IP addresses can be reused across millions of homes without conflict.

### Q3: What maps Private IP addresses to a single Public IP when accessing the Internet?
* **Answer:** **NAT (Network Address Translation)**. It runs on gateways/routers to modify network address information in IP packet headers while they are in transit across a traffic routing device.

---

## 6. Multiple-Choice Questions (MCQs) for Self-Assessment

#### Q1. How many layers are present in the practical TCP/IP model?
- A) 7
- B) 5
- C) 4
- D) 3
* **Correct Answer:** **C) 4**

#### Q2. Which of the following is a reserved Private IPv4 address range?
- A) `8.8.8.0/24`
- B) `192.168.0.0/16`
- C) `142.250.0.0/16`
- D) `200.1.1.0/24`
* **Correct Answer:** **B) `192.168.0.0/16`**

#### Q3. What is the bit length of an IPv6 address?
- A) 32 bits
- B) 64 bits
- C) 128 bits
- D) 256 bits
* **Correct Answer:** **C) 128 bits**

#### Q4. Which OSI layers are combined into the Application Layer of the TCP/IP model?
- A) Physical, Data Link, Network
- B) Transport, Network, Data Link
- C) Session, Presentation, Application
- D) Network, Transport, Application
* **Correct Answer:** **C) Session, Presentation, Application**

