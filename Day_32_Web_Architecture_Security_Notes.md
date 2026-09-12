Day 32: Web Architecture & Security Notes

## 1. Load Balancing Concepts
A **Load Balancer (LB)** distributes incoming network traffic across multiple backend servers to prevent overload, ensure high availability, and eliminate single points of failure.

### Core Benefits
* **High Availability (HA):** Automatically redirects traffic away from unhealthy instances.
* **Scalability:** Easily accommodates horizontal scaling by attaching new server instances.
* **Health Checks:** Continuously monitors target instances via specific protocols/ports (e.g., HTTP `GET /health`).

### Common Algorithms
* **Round Robin:** Distributes requests sequentially across servers.
* **Least Connections:** Routes traffic to the server with the fewest active client connections.
* **IP Hash:** Uses client IP to consistently map requests to a specific backend server.

### Layer 4 vs. Layer 7 Load Balancing
* **Layer 4 (Transport Layer):** Operates on IP addresses and TCP/UDP ports. Does not inspect application payload (e.g., AWS Network Load Balancer - NLB).
* **Layer 7 (Application Layer):** Inspects HTTP/HTTPS headers, cookies, and URL paths to make intelligent routing decisions (e.g., AWS Application Load Balancer - ALB, Nginx).

---

## 2. Reverse Proxies & Nginx Basics

### Forward Proxy vs. Reverse Proxy
* **Forward Proxy:** Positioned in front of clients to hide client identity and manage outbound internet requests.
* **Reverse Proxy:** Positioned in front of backend servers to hide backend infrastructure and manage inbound traffic.

### Purpose of Nginx as a Reverse Proxy
* **Anonymity & Security:** Hides backend internal IP addresses from the public internet.
* **SSL/TLS Termination:** Handles encryption/decryption at the proxy layer, offloading CPU-intensive cryptography from backend application servers.
* **Caching:** Serves cached static assets directly to clients without hitting backend services.

---

## 3. Firewalls: Network Firewall vs. Web Application Firewall (WAF)

### Network Firewall (Layer 3 & Layer 4)
* **Scope:** Inspects traffic based on Source/Destination IP addresses, Port numbers, and Protocols (TCP/UDP/ICMP).
* **Use Case:** Restricting SSH access (Port 22) or Database access (Port 3306) to explicit IP ranges (CIDR blocks).
* **Cloud Implementations:** AWS Security Groups, AWS Network ACLs (NACL), Azure Network Security Groups (NSG), Linux `iptables`/`UFW`.

### Web Application Firewall - WAF (Layer 7)
* **Scope:** Deeply inspects application layer traffic including HTTP/HTTPS headers, request body, URL parameters, and cookies.
* **Use Case:** Mitigates web application security vulnerabilities such as SQL Injection (SQLi), Cross-Site Scripting (XSS), and automated bot/DDoS attacks.
* **Cloud Implementations:** AWS WAF, Azure WAF, Cloudflare WAF.

---

## 4. SSL/TLS Certificates

### Definitions
* **SSL (Secure Sockets Layer):** Deprecated security protocol for encrypting internet traffic.
* **TLS (Transport Layer Security):** The modern, secure successor to SSL.

### Encryption Types
* **Symmetric Encryption:** Uses a single shared key for both encryption and decryption (Fast; used for session data transfer).
* **Asymmetric Encryption:** Uses a mathematically linked Key Pair — a **Public Key** for encryption and a **Private Key** for decryption (Slower; used for identity establishment).

### TLS Handshake Workflow
1. **Client Hello:** Client sends supported TLS versions and cipher suites.
2. **Server Hello & Certificate:** Server responds with chosen TLS parameters and its SSL Certificate (containing the Public Key).
3. **Validation & Key Exchange:** Client validates certificate authenticity against trusted CAs, generates a Pre-Master Secret, encrypts it with the Server's Public Key, and transmits it.
4. **Session Key Generation:** Both parties compute an identical Symmetric Session Key to securely encrypt subsequent HTTPS traffic.

### Certificate Types
* **CA-Signed Certificate:** Issued and digitally signed by trusted Certificate Authorities (e.g., Let's Encrypt, DigiCert). Universally trusted by web browsers for production environments.
* **Self-Signed Certificate:** Generated locally without CA validation. Used exclusively for internal staging, development, or testing environments; triggers browser trust warnings.

---

## 5. Public/Private SSH Key Pairs

### Architecture
SSH Key Authentication relies on Asymmetric Cryptography to securely authenticate users to remote servers without transmitting passwords over the network.

* **Public Key (`~/.ssh/authorized_keys` on Remote Server):** Stored on target hosts. Used by the server to encrypt a challenge during login. Safe to distribute publicly.
* **Private Key (`~/.ssh/id_rsa` or `.pem` on Local Client):** Kept strictly confidential on the local user's machine. Used to decrypt the login challenge and prove identity.

### Linux File Permission Standards
To prevent SSH client connection rejections due to unsafe file permissions:

```bash
# Secure the local private key (Read/Write for owner only)
chmod 600 ~/.ssh/id_rsa

# Secure the local .ssh configuration directory
chmod 700 ~/.ssh

# Secure the remote authorized_keys file
chmod 644 ~/.ssh/authorized_keys
