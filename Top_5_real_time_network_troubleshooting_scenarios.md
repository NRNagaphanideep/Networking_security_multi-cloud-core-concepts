eal-Time DevOps Network & Security Troubleshooting Scenarios: Runbook & Post-Mortem

> **Document Version:** 1.0  
> **Author:** DevOps Engineering Team  
> **Purpose:** Real-world incident response runbook covering complex production network failures, root cause analysis (RCA), and mitigation strategies.

---

## Scenario 1: The Ghost Connection Drop (TCP Keepalive & Idle Timeout Mismatch)

### 1. What / Where (The Symptom & Environment)
* **Environment:** Microservices architecture running on AWS EC2 behind an Application Load Balancer (ALB), communicating with an internal backend API service via long-lived gRPC/HTTP2 persistent connections.
* **Symptom:** Clients intermittently experience `504 Gateway Time-out` or abrupt connection resets (`RST`) during low-traffic periods (e.g., late at night or early morning), but everything works fine under heavy load.

### 2. Why (Root Cause Analysis)
* **The Underlying Mechanism:** AWS ALB has a hardcoded, unconfigurable **idle timeout of 60 seconds**. If a persistent TCP connection remains silent (no data packets flowing) for more than 60 seconds, the ALB silently drops its connection state tracking table entry. 
* **The Failure:** When a subsequent request comes in after 60 seconds, the client sends a packet down the existing TCP socket. The backend server (or firewall in between) still considers the socket open and responds, but the ALB rejects the packet because it no longer recognizes the connection state, sending a TCP `RST` or dropping it entirely, resulting in a timeout.

### 3. How (Step-by-Step Troubleshooting & Resolution)
1. **Packet Capture & Log Inspection:** 
   Run `tcpdump` on the backend server interface to inspect incoming packets:
   ```bash
   sudo tcpdump -nnvvv -i eth0 'tcp[tcpflags] & (tcp-rst|tcp-fin) != 0'
   ```
   *Observation:* Frequent unprompted `RST` packets originating from the gateway IP.
2. **Reviewing Nginx / Application Settings:** Check backend proxy or server keepalive configurations.
3. **The Fix:** Configure TCP Keepalive probes at the operating system kernel level and application connection pool level so that idle packets are sent *before* the 60-second ALB threshold triggers:
   * **Linux Kernel Tweaks (`/etc/sysctl.conf`):**
     ```ini
     net.ipv4.tcp_keepalive_time = 30
     net.ipv4.tcp_keepalive_intvl = 10
     net.ipv4.tcp_keepalive_probes = 3
     ```
     Apply changes: `sudo sysctl -p`
   * **Nginx Upstream Fix (if proxying):**
     ```nginx
     keepalive_timeout 65;
     keepalive_requests 1000;
     ```

---

## Scenario 2: The Silent Black Hole (Path MTU Discovery & ICMP Black Hole)

### 1. What / Where (The Symptom & Environment)
* **Environment:** Hybrid cloud architecture connecting an on-premises data center to an AWS VPC via an IPSec VPN tunnel.
* **Symptom:** Small requests (like HTTP `GET` for static pages or basic `curl` health checks) succeed instantly, but large requests (like POST payloads > 1400 bytes, database query dumps, or SSH file SCP transfers) hang indefinitely and eventually time out.

### 2. Why (Root Cause Analysis)
* **The Underlying Mechanism:** Standard Ethernet MTU is 1500 bytes. Encapsulating packets inside an IPSec VPN tunnel adds encryption header overhead, reducing the effective path MTU (e.g., down to 1380 bytes).
* **The Failure:** When a large packet (1500 bytes) hits the VPN gateway, it exceeds the tunnel MTU. The gateway drops the packet and generates an **ICMP Type 3 Code 4 ("Fragmentation Needed")** message back to the sender. However, strict corporate firewalls or cloud security groups often block *all* incoming ICMP traffic for "security hardening." Because the sender never receives the ICMP fragmentation notice, Path MTU Discovery (PMTUD) fails completely, creating a **PMTUD Black Hole**.

### 3. How (Step-by-Step Troubleshooting & Resolution)
1. **Verify MTU Drop via Ping:**
   ```bash
   ping -M do -s 1472 <destination_ip>
   ```
   *Observation:* Packets fail with `Message too long / FragNeeded` when size exceeds threshold.
2. **Trace Path MTU:** Use `traceroute` or `tracepath` to detect MTU limitations.
3. **The Fix:** Implement **TCP MSS (Maximum Segment Size) Clamping** on the edge firewall/VPN router so that packets automatically negotiate a smaller segment size regardless of ICMP blocking:
   * On Linux iptables firewall / router:
     ```bash
     sudo iptables -A FORWARD -p tcp --tcp-flags SYN,RST SYN -j TCPMSS --clamp-mss-to-pmtu
     ```
   * On cloud VPC interfaces, configure interface MTU explicitly to match tunnel overhead limits.

---

## Scenario 3: The Exhausted Kernel (Conntrack Table Full & Firewall Drops)

### 1. What / Where (The Symptom & Environment)
* **Environment:** High-throughput API gateway cluster running on Ubuntu Linux handling tens of thousands of concurrent short-lived HTTP connections from mobile client apps.
* **Symptom:** Applications suddenly start throwing intermittent connection errors. System log (`dmesg` or `/var/log/kern.log`) shows massive warning messages: `nf_conntrack: table full, dropping packet`. CPU usage spikes, and outgoing connections fail.

### 2. Why (Root Cause Analysis)
* **The Underlying Mechanism:** Linux netfilter stateful firewall maintains a connection tracking table (`nf_conntrack`) to remember the state of every active TCP, UDP, and ICMP flow. 
* **The Failure:** Under high request concurrency or a slow-loris style connection flood, the number of active entries exceeds the default kernel limit (`nf_conntrack_max`). Once the table is completely full, the kernel refuses to track any new socket connections and silently drops incoming SYN packets, causing a complete network denial of service for new clients while established connections struggle.

### 3. How (Step-by-Step Troubleshooting & Resolution)
1. **Diagnose Table Utilization:**
   ```bash
   sudo sysctl net.netfilter.nf_conntrack_count
   sudo sysctl net.netfilter.nf_conntrack_max
   ```
2. **Inspect Kernel Logs:**
   ```bash
   dmesg | grep -i conntrack
   ```
3. **The Fix:** Increase the maximum conntrack table size and shorten TCP timeout intervals to purge dead connection states faster:
   * **Permanent Kernel Tuning (`/etc/sysctl.d/99-conntrack.conf`):**
     ```ini
     net.netfilter.nf_conntrack_max = 524288
     net.netfilter.nf_conntrack_buckets = 131072
     net.netfilter.ip_conntrack_tcp_timeout_established = 600
     net.netfilter.nf_conntrack_tcp_timeout_time_wait = 30
     ```
   * Apply changes instantly:
     ```bash
     sudo sysctl --system
     ```

---

## Scenario 4: The Poisoned Route (ARP Spoofing & Lateral Movement Breach)

### 1. What / Where (The Symptom & Environment)
* **Environment:** On-premises bare-metal developer staging subnet shared across multiple engineering testing VMs.
* **Symptom:** Network monitoring alerts flag unusual traffic redirection. Internal staging applications experience intermittent latency spikes, and security teams detect unauthorized packet sniffing on port 22/3306 between local servers.

### 2. Why (Root Cause Analysis)
* **The Underlying Mechanism:** ARP (Address Resolution Protocol) is an inherently stateless, unauthenticated Layer 2 protocol. Any device on the local network can broadcast an unsolicited ARP reply ("gratuitous ARP") claiming to be the default gateway or database server.
* **The Failure:** A compromised test container on the same subnet broadcasted forged ARP announcements, mapping the gateway's IP address to its own malicious MAC address. This caused all neighboring servers to redirect their default gateway traffic through the compromised host (Man-in-the-Middle attack).

### 3. How (Step-by-Step Troubleshooting & Resolution)
1. **Inspect Local ARP Cache for Anomalies:**
   ```bash
   ip neigh show
   ```
   *Observation:* Multiple distinct IP addresses sharing the exact same physical MAC address.
2. **Trace the Culprit MAC Address on Switch / Host:**
   ```bash
   arp -an
   ```
3. **The Fix:**
   * **Immediate Mitigation:** Clear poisoned cache and add static ARP entries for critical infrastructure gateways:
     ```bash
     sudo ip neigh flush all
     sudo arp -s <gateway_ip> <gateway_mac>
     ```
   * **Long-Term Hardening:** Enable **Dynamic ARP Inspection (DAI)** and Port Security on enterprise managed network switches, and segment development subnets into isolated VLANs.

---

## Scenario 5: The Expired Handshake (CAS/Intermediate Chain Misconfiguration & TLS Failures)

### 1. What / Where (The Symptom & Environment)
* **Environment:** Public-facing production e-commerce web cluster fronted by an Nginx reverse proxy serving HTTPS traffic with a newly rotated SSL certificate.
* **Symptom:** Internal corporate desktop browsers load the website successfully, but external mobile apps and strict API clients (like Python scripts using `requests`) throw `SSL: CERTIFICATE_VERIFY_FAILED` or `SEC_ERROR_UNKNOWN_ISSUER` errors.

### 2. Why (Root Cause Analysis)
* **The Underlying Mechanism:** TLS certificate validation requires a complete trust chain: Leaf Certificate $
ightarrow$ Intermediate CA $
ightarrow$ Root CA. Browsers often cache intermediate certificates from previous visits, masking missing server configurations.
* **The Failure:** The system administrator configured Nginx with *only* the leaf certificate (`ssl_certificate`), omitting the mandatory **Intermediate Certificate Authority bundle**. While desktop browsers cached the intermediate cert from older visits, fresh external clients had no way to bridge trust back to the trusted Root CA, causing handshake rejection.

### 3. How (Step-by-Step Troubleshooting & Resolution)
1. **Diagnose Trust Chain Remotely:**
   ```bash
   openssl s_client -connect example.com:443 -servername example.com
   ```
   *Observation:* Look for `Verify return code: 21 (unable to verify the first certificate)`.
2. **Inspect Certificate Chain File:** Verify that the `.crt` file contains both the server certificate AND the intermediate CA certificate concatenated together.
3. **The Fix:**
   * Recreate the full certificate chain file:
     ```bash
     cat server.crt intermediate.crt > /etc/ssl/certs/fullchain.crt
     ```
   * Update Nginx configuration (`/etc/nginx/sites-available/prod.conf`):
     ```nginx
     ssl_certificate /etc/ssl/certs/fullchain.crt;
     ssl_certificate_key /etc/ssl/private/server.key;
     ```
   * Test syntax and reload Nginx:
     ```bash
     sudo nginx -t && sudo systemctl reload nginx
     ```

---
