# Terminal-Based Linux Server Monitoring Tools

A comprehensive guide to CLI monitoring tools for headless Linux servers, perfect for Ubuntu Server and Kubernetes node management.

---

## System Resource Monitors (htop alternatives)

### 1. **htop** - Enhanced Process Viewer
The gold standard for interactive process monitoring.

```bash
# Install
sudo apt install htop

# Run
htop

# Key features:
# - Color-coded CPU/memory bars
# - Tree view of processes (F5)
# - Filter processes (F4)
# - Kill processes (F9)
# - Sort by various metrics (F6)
# - Search processes (F3)
```

**Best for:** Interactive process management, quick system overview

---

### 2. **btop** - Modern Resource Monitor
Beautiful, feature-rich alternative with network and disk stats.

```bash
# Install
sudo snap install btop
# Or from source
wget https://github.com/aristocratos/btop/releases/latest/download/btop-x86_64-linux-musl.tbz
tar -xjf btop-x86_64-linux-musl.tbz
sudo cp btop/bin/btop /usr/local/bin/

# Run
btop
```

**Features:**
- Mouse support
- CPU, memory, disk, network in single view
- Process filtering and sorting
- Beautiful ASCII graphs
- Theme customization

**Best for:** Comprehensive single-screen monitoring, modern interface

---

### 3. **glances** - Cross-Platform Monitor
Python-based tool with extensive plugin support.

```bash
# Install
sudo apt install glances
# Or via pip
pip install glances

# Run
glances

# Run in web mode (accessible via browser)
glances -w
# Access at http://server-ip:61208

# Run as server for remote monitoring
glances -s
# Connect from client: glances -c server-ip

# Export to various backends
glances --export influxdb
glances --export prometheus
```

**Features:**
- Auto-adapts to terminal size
- Plugin architecture (Docker, Kubernetes)
- Alert thresholds
- Export to monitoring systems
- Web interface option

**Best for:** Remote monitoring, integration with monitoring stacks

---

### 4. **top** - Classic Process Monitor
Pre-installed on every Linux system.

```bash
# Run
top

# Useful flags
top -d 2          # 2-second refresh
top -u username   # Filter by user
top -p 1234,5678  # Monitor specific PIDs

# Interactive commands:
# 1     - Show individual CPU cores
# P     - Sort by CPU
# M     - Sort by Memory
# k     - Kill process
# r     - Renice process
# W     - Save configuration
```

**Best for:** Universal availability, scripting, minimal dependencies

---

### 5. **atop** - Advanced System Monitor
Records historical system performance.

```bash
# Install
sudo apt install atop

# Run live
atop

# View historical data
atop -r /var/log/atop/atop_20241217

# View specific metrics
atop -m    # Memory emphasis
atop -d    # Disk emphasis
atop -n    # Network emphasis
```

**Features:**
- Logs system activity every 10 minutes
- Historical playback
- Resource accounting per process
- Detects disappeared processes

**Best for:** Performance analysis, troubleshooting historical issues

---

## Network Monitoring

### 6. **iftop** - Network Bandwidth Monitor
Real-time network traffic by connection.

```bash
# Install
sudo apt install iftop

# Run
sudo iftop -i eth0

# Useful options
sudo iftop -n           # No DNS resolution
sudo iftop -B           # Show bytes instead of bits
sudo iftop -P           # Show ports
sudo iftop -f "port 80" # Filter traffic
```

**Best for:** Identifying bandwidth hogs, monitoring specific interfaces

---

### 7. **nload** - Network Interface Load
Simple bandwidth monitor with graphs.

```bash
# Install
sudo apt install nload

# Run
nload

# Monitor specific interface
nload eth0

# Multiple interfaces
nload eth0 eth1
```

**Best for:** Quick bandwidth visualization, simple interface

---

### 8. **nethogs** - Per-Process Bandwidth
Shows which processes are using bandwidth.

```bash
# Install
sudo apt install nethogs

# Run
sudo nethogs

# Monitor specific interface
sudo nethogs eth0

# Interactive commands:
# m - Change units (KB/s, MB/s)
# r - Sort by received
# s - Sort by sent
# q - Quit
```

**Best for:** Identifying which application is using network

---

### 9. **vnstat** - Network Traffic Statistics
Long-term bandwidth logging and statistics.

```bash
# Install
sudo apt install vnstat

# Setup
sudo systemctl enable --now vnstat

# View stats
vnstat              # Summary
vnstat -d           # Daily stats
vnstat -m           # Monthly stats
vnstat -h           # Hourly stats
vnstat -l           # Live traffic
vnstat --top        # Top traffic days

# Monitor specific interface
vnstat -i eth0
```

**Best for:** Long-term bandwidth trends, usage reports

---

## Disk I/O Monitoring

### 10. **iotop** - Disk I/O by Process
Shows which processes are performing I/O.

```bash
# Install
sudo apt install iotop

# Run
sudo iotop

# Useful options
sudo iotop -o        # Only show active I/O
sudo iotop -b -n 3   # Batch mode, 3 iterations
sudo iotop -P        # Show processes instead of threads
sudo iotop -a        # Accumulated I/O
```

**Best for:** Identifying disk-heavy processes, I/O troubleshooting

---

### 11. **iostat** - Disk Statistics
Part of sysstat package, shows disk performance.

```bash
# Install
sudo apt install sysstat

# Run
iostat

# Extended statistics
iostat -x 2 5    # Extended, 2-sec interval, 5 iterations

# Per-device stats
iostat -d        # Device only
iostat -p ALL    # All partitions

# CPU and disk together
iostat -xz 1     # Skip devices with no activity
```

**Output explained:**
```
Device    r/s   w/s   rkB/s   wkB/s  await  %util
sda      12.3  45.6   234.5   1234   8.2    23.4
```
- **r/s, w/s**: reads/writes per second
- **rkB/s, wkB/s**: KB read/written per second
- **await**: average wait time (ms)
- **%util**: device utilization

**Best for:** Disk performance analysis, bottleneck identification

---

### 12. **dstat** - Versatile Resource Monitor
Combines vmstat, iostat, netstat, and more.

```bash
# Install
sudo apt install dstat

# Run
dstat

# Common combinations
dstat -cdngy       # CPU, disk, network, page, system
dstat --top-bio    # Top block I/O processes
dstat --top-cpu    # Top CPU processes
dstat --top-mem    # Top memory processes

# Export to CSV
dstat --output system.csv 5 60  # 5-sec interval, 60 iterations
```

**Best for:** Comprehensive monitoring, data collection for analysis

---

## System Performance Analysis

### 13. **vmstat** - Virtual Memory Statistics
System-wide performance metrics.

```bash
# Run
vmstat 2 10    # 2-second interval, 10 iterations

# Extended mode
vmstat -s      # Statistics summary
vmstat -d      # Disk statistics
vmstat -p /dev/sda1  # Partition stats
```

**Output columns:**
```
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 1  0      0 512345  67890 234567    0    0    12    45  234 567 15  5 78  2  0
```

**Best for:** Overall system performance, memory pressure analysis

---

### 14. **mpstat** - Multi-Processor Statistics
Per-CPU core statistics.

```bash
# Install (part of sysstat)
sudo apt install sysstat

# Run
mpstat

# Per-CPU breakdown
mpstat -P ALL 2 5    # All CPUs, 2-sec interval

# Individual CPU
mpstat -P 0,1        # CPUs 0 and 1
```

**Best for:** CPU load balancing, per-core performance

---

### 15. **pidstat** - Process Statistics
Detailed per-process resource usage.

```bash
# Install (part of sysstat)
sudo apt install sysstat

# CPU usage
pidstat 2 5

# Memory usage
pidstat -r 2 5

# Disk I/O
pidstat -d 2 5

# Context switches
pidstat -w 2 5

# Specific process
pidstat -p 1234

# Command name filter
pidstat -C kubelet
```

**Best for:** Detailed process analysis, resource attribution

---

## Log Monitoring

### 16. **lnav** - Log Navigator
Advanced log file viewer with filtering.

```bash
# Install
sudo apt install lnav

# View logs
lnav /var/log/syslog

# Multiple files
lnav /var/log/syslog /var/log/auth.log

# Follow in real-time
lnav -t /var/log/syslog

# View journalctl
journalctl -f | lnav
```

**Features:**
- Syntax highlighting
- Automatic format detection
- SQL queries on logs
- Timeline view
- Filtering and searching

**Best for:** Log analysis, troubleshooting from logs

---

### 17. **multitail** - Multiple Log Files
View multiple log files simultaneously.

```bash
# Install
sudo apt install multitail

# View multiple logs
multitail /var/log/syslog /var/log/auth.log

# With labels
multitail -l 'kubectl logs pod1' -l 'kubectl logs pod2'

# Color schemes
multitail -c /var/log/syslog

# Merge logs
multitail -s 2 /var/log/syslog /var/log/auth.log
```

**Best for:** Monitoring multiple services simultaneously

---

## Container-Specific Tools

### 18. **ctop** - Container Top
Docker/container resource monitor.

```bash
# Install
sudo wget https://github.com/bcicen/ctop/releases/download/v0.7.7/ctop-0.7.7-linux-amd64 -O /usr/local/bin/ctop
sudo chmod +x /usr/local/bin/ctop

# Run
ctop

# Filter by name
ctop -f name=nginx

# Connector options
ctop -i      # Interval
ctop -a      # Show all containers
```

**Best for:** Docker container monitoring, Kubernetes node analysis

---

### 19. **k9s** - Kubernetes CLI
Terminal UI for Kubernetes clusters.

```bash
# Install
curl -sS https://webinstall.dev/k9s | bash

# Run
k9s

# Specific namespace
k9s -n kube-system

# Read-only mode
k9s --readonly

# Key commands:
# :pods        - View pods
# :svc         - View services
# :deploy      - View deployments
# :nodes       - View nodes
# :top node    - Node metrics
```

**Best for:** Kubernetes cluster management, your HomeLab!

---

## All-in-One Solutions

### 20. **Netdata** - Real-Time Performance Monitor
Comprehensive monitoring with web interface.

```bash
# Install
bash <(curl -Ss https://my-netdata.io/kickstart.sh)

# Access via web browser
# http://server-ip:19999

# View in terminal (limited)
curl http://localhost:19999/api/v1/allmetrics
```

**Features:**
- 1-second granularity
- 2000+ metrics
- Machine learning anomaly detection
- Zero configuration
- Historical data (RAM-based)

**Best for:** Comprehensive monitoring, visual dashboards

---

## Specialized Tools

### 21. **bpytop** - Python Port of btop
Resource monitor with Python flexibility.

```bash
# Install
sudo apt install bpytop

# Run
bpytop
```

---

### 22. **nmon** - Performance Monitor
IBM's performance monitoring tool.

```bash
# Install
sudo apt install nmon

# Run interactively
nmon

# Recording mode
nmon -f -s 60 -c 1440    # 60-sec interval, 24 hours

# Keys to press:
# c - CPU
# m - Memory
# d - Disk
# n - Network
# t - Top processes
```

**Best for:** Performance data collection, capacity planning

---

## Kubernetes-Specific Monitoring

### 23. **kubectl top**
Built-in resource metrics.

```bash
# Node metrics
kubectl top nodes

# Pod metrics
kubectl top pods -A

# Container metrics
kubectl top pods --containers

# Sort by CPU
kubectl top pods -A --sort-by=cpu

# Sort by memory
kubectl top pods -A --sort-by=memory
```

**Requires:** metrics-server installed in cluster

---

### 24. **kubetail** - Aggregate Pod Logs
Tail multiple pod logs simultaneously.

```bash
# Install
wget https://raw.githubusercontent.com/johanhaleby/kubetail/master/kubetail
chmod +x kubetail
sudo mv kubetail /usr/local/bin/

# Use
kubetail nginx
kubetail -n kube-system coredns
kubetail -l app=nginx    # By label
```

---

## Quick Comparison Table

| Tool | Purpose | Resource Usage | Learning Curve | Best Use Case |
|------|---------|----------------|----------------|---------------|
| **htop** | Process monitor | Low | Easy | General process management |
| **btop** | All-in-one | Medium | Easy | Beautiful comprehensive view |
| **glances** | System monitor | Medium | Medium | Remote monitoring |
| **iftop** | Network monitor | Low | Easy | Bandwidth analysis |
| **nethogs** | Process network | Low | Easy | Per-process bandwidth |
| **iotop** | Disk I/O | Low | Easy | I/O troubleshooting |
| **dstat** | Multi-purpose | Low | Medium | Data collection |
| **k9s** | Kubernetes | Low | Medium | K8s cluster management |
| **ctop** | Containers | Low | Easy | Container monitoring |
| **nmon** | Performance | Low | Medium | Long-term monitoring |

---

## Installation Script for Your HomeLab

```bash
#!/bin/bash
# Install essential monitoring tools for Kubernetes HomeLab

echo "Installing system monitors..."
sudo apt update
sudo apt install -y htop glances atop

echo "Installing network monitors..."
sudo apt install -y iftop nethogs nload vnstat

echo "Installing disk monitors..."
sudo apt install -y iotop sysstat

echo "Installing log tools..."
sudo apt install -y lnav multitail

echo "Installing all-in-one tools..."
sudo apt install -y dstat nmon

echo "Installing btop..."
sudo snap install btop

echo "Installing k9s..."
curl -sS https://webinstall.dev/k9s | bash

echo "Installing ctop..."
sudo wget https://github.com/bcicen/ctop/releases/download/v0.7.7/ctop-0.7.7-linux-amd64 -O /usr/local/bin/ctop
sudo chmod +x /usr/local/bin/ctop

echo "Enabling vnstat service..."
sudo systemctl enable --now vnstat

echo "Installation complete! Tools installed:"
echo "- htop, glances, atop (system monitoring)"
echo "- iftop, nethogs, nload, vnstat (network)"
echo "- iotop, iostat, dstat (disk I/O)"
echo "- lnav, multitail (logs)"
echo "- k9s (Kubernetes)"
echo "- ctop (containers)"
```

---

## Recommendations for Your K3s HomeLab

**Essential Daily Tools:**
1. **k9s** - Your primary Kubernetes interface (you're already using this!)
2. **htop** - Quick process checks
3. **iftop** - Network traffic monitoring

**Troubleshooting Tools:**
1. **glances** - Comprehensive system overview
2. **iotop** - When pods have I/O issues
3. **nethogs** - Network bandwidth per process

**Performance Analysis:**
1. **dstat** - Collecting performance data
2. **pidstat** - Detailed process metrics
3. **atop** - Historical performance review

**For Your Interview Portfolio:**
Demonstrate familiarity with these tools to showcase Linux administration skills. Most impressive: k9s, glances, and the ability to use multiple tools together for troubleshooting.