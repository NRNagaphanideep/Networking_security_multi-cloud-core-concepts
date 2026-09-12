Day 33: Linux System Administration & Advanced Troubleshooting Notes

## 1. Process Management Commands

### `ps` (Process Status)
Displays a static snapshot of current running processes.
* `ps aux`: Displays all running processes for all users with detailed resource info.
* `ps -ef`: Displays full process format including Parent Process ID (PPID).

### `top` (Table of Processes)
Provides a dynamic, real-time view of running system processes, CPU, and RAM usage.
* Press `M`: Sort by Memory usage.
* Press `P`: Sort by CPU usage.

### `htop` (Interactive Process Viewer)
An enhanced, colorized, and interactive process manager allowing mouse navigation, process sorting (F6), and direct termination (F9).

### Process Signals & Termination
* `kill -15 <PID>`: **SIGTERM** — Gracefully terminates a process allowing cleanup.
* `kill -9 <PID>`: **SIGKILL** — Forces immediate termination of a process.
* `pkill -9 <Process_Name>`: Force-kills all processes matching the specified name.

### Priority Control (`nice` & `renice`)
Determines CPU scheduling priority for processes (Range: `-20` highest priority to `19` lowest priority).
* Launching with modified priority: `nice -n 10 ./backup-script.sh`
* Modifying running process priority: `renice -n -5 -p 1234`

---

## 2. System Resource Monitoring

### `df` (Disk Free)
Displays file system disk space usage.
* Command: `df -h` (`-h` for Human-Readable MB/GB formats).

### `du` (Disk Usage)
Estimates file and directory space utilization.
* Command: `du -sh /var/log` (`-s` for summary, `-h` for human-readable).

### `free` (Free Memory)
Displays total, used, and available physical RAM and Swap memory.
* Command: `free -h`

### `uptime` (System Uptime & Load Average)
Shows how long the system has been running alongside 1, 5, and 15-minute load averages.

### `vmstat` (Virtual Memory Statistics)
Reports information about processes, memory, paging, block I/O, traps, and CPU activity.
* Command: `vmstat 2 5` (Refreshes every 2 seconds for 5 iterations).

#### Header Breakdown (`vmstat`):
* **procs:** `r` (Running/Runnable processes waiting for CPU), `b` (Blocked processes waiting on I/O).
* **memory:** `swpd` (Virtual memory used), `free` (Idle RAM), `buff` (Buffer memory), `cache` (Cached file data).
* **swap:** `si` (Swap In per sec), `so` (Swap Out per sec).
* **io:** `bi` (Blocks received from device per sec), `bo` (Blocks sent to device per sec).
* **system:** `in` (Interrupts per sec), `cs` (Context switches per sec).
* **cpu (%):** `us` (User application time), `sy` (Kernel time), `id` (Idle time), `wa` (Waiting on I/O), `st` (Steal time stolen by hypervisor).

### `iostat` (Input/Output Statistics)
Monitors system input/output device loading and disk transfer speeds.
* Command: `iostat -xz 1`

---

## 3. Log Analysis & Management

### Core Log Locations (`/var/log`)
* `/var/log/syslog`: General system events and service warnings.
* `/var/log/auth.log`: Authentication attempts, SSH logins, and `sudo` executions.

### Systemd Journal Viewer (`journalctl`)
Centralized logging utility for systemd services.
```bash
# Filter logs by service unit
journalctl -u nginx -n 50 --no-pager

# Filter logs by priority level
journalctl -p err

# Real-time live log tailing
journalctl -f
``` 
### Log Rotation Utility (logrotate)
Prevents disk capacity exhaustion by compressing and removing obsolete log files.

###  Config Path: /etc/logrotate.d/

Sample Configuration (/etc/logrotate.d/nginx):
```
/var/log/nginx/*.log {
    daily
    rotate 7
    compress
    missingok
    notifempty
}
```
### 4. Systemd Services & Daemon Management
Controlling Services (systemctl)
#### Check service status: systemctl status nginx

#### Enable auto-start on boot: systemctl enable nginx

#### Restart service: systemctl restart nginx
Custom Systemd Unit Creation
```
Service File Location: /etc/systemd/system/todo-app.service

[Unit]
Description=My Custom Node.js Web Application
After=network.target

[Service]
Type=simple
User=ubuntu
WorkingDirectory=/var/www/todo-app
ExecStart=/usr/bin/node /var/www/todo-app/server.js
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

### 5. Advanced Network Troubleshooting Commands
ss (Socket Statistics)
Displays active listening network sockets and open ports.

Command: ss -tulpn

-t: TCP sockets

-u: UDP sockets

-l: Listening sockets

-p: Show process using socket

-n: Numeric port numbers

lsof (List Open Files)
Identifies processes associated with specific open network ports.

Command: lsof -i :80

DNS Diagnostics (dig & nslookup)
Performs DNS record lookups and troubleshooting.

Command: dig google.com +short

Connectivity & Packet Tracing
ping <IP/Domain>: Tests network layer reachability and latency.

traceroute <IP/Domain>: Traces the hop-by-hop packet route to a destination.

curl -I <URL>: Inspects HTTP/HTTPS response headers.

tcpdump (Packet Analyzer)
Captures raw network traffic passing through network interfaces.

Command: sudo tcpdump -i eth0 -c 2 port 80

Host Firewalls (UFW & iptables)
Enable port access via UFW: sudo ufw allow 80/tcp

Inspect iptables rules: sudo iptables -L -n -v

-------

