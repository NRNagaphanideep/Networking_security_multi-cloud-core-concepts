
rking Protocols & Terminology: Full Forms & Explanations Reference Guide

---

## 1. Web & Application Transfer Protocols

### HTTP
* **Full Form:** Hypertext Transfer Protocol
* **Description:** The foundational application-layer protocol used by the World Wide Web to load web pages using plain-text communication.
* **Default Port:** `80` (TCP)

### HTTPS
* **Full Form:** Hypertext Transfer Protocol Secure
* **Description:** The encrypted version of HTTP that uses SSL/TLS certificates to secure sensitive data transmitted between a client browser and a web server.
* **Default Port:** `443` (TCP)

### gRPC
* **Full Form:** gRPC Remote Procedure Calls (where "gRPC" recursively stands for **gRPC Remote Procedure Calls**, originally created by Google)
* **Description:** A high-performance, open-source universal Remote Procedure Call (RPC) framework that uses HTTP/2 for transport and Protocol Buffers for data serialization, widely used in microservice architectures.
* **Default Port:** Varies (typically uses standard HTTP/2 ports like `50051`)

---

## 2. Remote Access & File Transfer Protocols

### SSH
* **Full Form:** Secure Shell
* **Description:** A cryptographic network protocol used for secure operating system logins, remote command execution, and file transfers over an unsecured network.
* **Default Port:** `22` (TCP)

### FTP
* **Full Form:** File Transfer Protocol
* **Description:** A standard network protocol used for transferring computer files between a client and a server on a computer network.
* **Default Ports:** `20` (Data Transfer) & `21` (Control Connection) (TCP)

---

## 3. Email & Communication Protocols

### SMTP
* **Full Form:** Simple Mail Transfer Protocol
* **Description:** An Internet standard communication protocol used for transmitting electronic mail (email) messages across IP networks.
* **Default Ports:** `25` (Standard/Unencrypted), `587` (Secure Submission), `465` (Legacy SMTPS)

### VoIP
* **Full Form:** Voice over Internet Protocol
* **Description:** A technology that allows voice calls, multimedia sessions, and real-time communication over Internet Protocol (IP) networks instead of traditional telephone lines.
* **Default Ports:** `5060` / `5061` (SIP Signaling) & `16384` to `32767` (RTP Audio Streams)

---

## 4. Core Infrastructure & Address Management Protocols

### DNS
* **Full Form:** Domain Name System
* **Description:** The hierarchical naming system that translates human-friendly domain names (e.g., `github.com`) into computer-readable IP addresses (e.g., `140.82.121.4`).
* **Default Port:** `53` (UDP/TCP)

### DHCP
* **Full Form:** Dynamic Host Configuration Protocol
* **Description:** A network management protocol that automatically configures and assigns IP addresses, subnet masks, default gateways, and DNS settings to devices on a network.
* **Default Ports:** `67` (DHCP Server) & `68` (DHCP Client) (UDP)

---

## 5. Security & Tunneling Protocols

### SSL / TLS
* **Full Forms:**
  * **SSL:** Secure Sockets Layer
  * **TLS:** Transport Layer Security
* **Description:** Cryptographic protocols designed to provide communications security over a computer network. TLS is the modern, secure successor to the now-deprecated SSL.
* **Usage:** Secures HTTPS, SMTPS, FTPS, and custom application traffic.

### PPTP
* **Full Form:** Point-to-Point Tunneling Protocol
* **Description:** An obsolete method for implementing Virtual Private Networks (VPNs) that creates a secure tunnel over an IP network, largely replaced by safer modern VPN protocols like OpenVPN and WireGuard.
* **Default Port:** `1723` (TCP)

---

## 6. Diagnostic & Low-Level Network Protocols

### ICMP
* **Full Form:** Internet Control Message Protocol
* **Description:** A network layer protocol used by network devices (like routers) to send operational information and error messages (e.g., indicating that a requested service is unavailable or a host is unreachable). Used by utilities like `ping` and `traceroute`.
* **Port:** Does not use TCP or UDP port numbers (runs directly over IP Protocol `1`).

### ARP
* **Full Form:** Address Resolution Protocol
* **Description:** A communication protocol used for discovering the Link Layer address, such as a MAC (Media Access Control) address, associated with a given IPv4 address.
* **Layer:** Works between Layer 2 (Data Link) and Layer 3 (Network) of the OSI model.
