# Installing Proxmox VE on Beelink SER5 Pro Mini PC: A Complete Guide

**Author:** Mahmood  
**Date:** December 25, 2024  
**Hardware:** Beelink SER5 Pro (AMD Ryzen 5 5500U, 32GB RAM, 500GB NVMe)  
**Software:** Proxmox VE 8.x
---

## Table of Contents

1. [Introduction](#introduction)
2. [Hardware Overview](#hardware-overview)
3. [Prerequisites](#prerequisites)
4. [Installation Process](#installation-process)
5. [Post-Installation Configuration](#post-installation-configuration)
6. [Network Configuration](#network-configuration)
7. [Important Note: WiFi on Proxmox](#important-note-wifi-on-proxmox)
8. [Accessing Proxmox Remotely](#accessing-proxmox-remotely)
9. [Next Steps](#next-steps)
10. [Troubleshooting](#troubleshooting)

---

## Introduction

This guide documents the complete process of installing Proxmox VE on a Beelink SER5 Pro mini PC to create a production-grade Kubernetes homelab environment. The setup is specifically designed for:

- **CKA (Certified Kubernetes Administrator) exam preparation**
- **Multi-node Kubernetes cluster deployment**
- **Learning enterprise-grade infrastructure tools**

### Why Proxmox?

- **Type 1 Hypervisor:** Bare-metal virtualization for optimal performance
- **Enterprise Features:** Snapshots, backups, clustering, HA support
- **Web-Based Management:** Accessible from any device
- **Free and Open Source:** No licensing costs
- **KVM/LXC Support:** Run both VMs and containers

---

## Hardware Overview

### Beelink SER5 Pro Specifications

```
CPU:     AMD Ryzen 5 5500U (6 cores / 12 threads, up to 4.0GHz)
RAM:     32GB DDR4 (Usable 27GiB)
Storage: 500GB NVMe SSD (476GB usable)
Network: 1x Gigabit Ethernet (Realtek)
```

### Resource Allocation Plan

```
Proxmox Host:     ~7GB RAM reserved
Kubernetes VMs:   ~20GB RAM allocated
  - Control Plane: 6GB RAM, 3 vCPU, 50GB disk
  - Worker 1:      7GB RAM, 3 vCPU, 100GB disk
  - Worker 2:      7GB RAM, 3 vCPU, 100GB disk
```

**Power Consumption:** ~25-35W average (€6-10/month at German electricity rates)


## Prerequisites

### Required Items

1. **Hardware:**
   - Beelink SER5 Pro mini PC
   - Monitor + HDMI cable (temporary, only for initial setup)
   - USB keyboard (temporary)
   - Ethernet cable
   - USB flash drive (8GB minimum) for Proxmox installer

2. **Software Downloads:**
   - Proxmox VE ISO: [https://www.proxmox.com/en/downloads](https://www.proxmox.com/en/downloads)
   - Rufus (Windows): [https://rufus.ie/](https://rufus.ie/) for creating bootable USB
   - Or balenaEtcher (cross-platform): [https://etcher.balena.io/](https://etcher.balena.io/)

3. **Network Information:**
   - Router IP address (usually 192.168.1.1 or 192.168.178.1)
   - Desired static IP for Proxmox (e.g., 192.168.178.33)
   - Network subnet mask (usually 255.255.255.0 or /24)

---

## Installation Process

### Step 1: Create Proxmox Bootable USB

**On Windows using Rufus:**

1. Download Proxmox VE ISO from official website
2. Insert USB flash drive (all data will be erased!)
3. Launch Rufus
4. Configure settings:
   ```
   Device:           [Your USB drive]
   Boot selection:   [Proxmox VE ISO]
   Partition scheme: MBR
   Target system:    BIOS or UEFI
   File system:      FAT32
   ```
5. Click **START**
6. When prompted, select **"Write in ISO Image mode"** (recommended)
7. Wait for completion (~5 minutes)

### Step 2: Configure BIOS Settings

1. **Access BIOS:**
   - Power on Beelink
   - Immediately press **DEL** or **F2** repeatedly
   - Or from Windows: Settings → Recovery → Advanced startup → UEFI Firmware Settings

2. **Critical BIOS Settings:**

   ```
   Boot Mode:
     → Set to: UEFI (or UEFI with CSM)
     → Disable: Secure Boot
   
   Virtualization (CRITICAL):
     → AMD-V / SVM Mode: ENABLED
     → IOMMU: ENABLED (if available)
   
   Boot Priority:
     → 1st Boot Device: USB Device
     → 2nd Boot Device: NVMe SSD
   
   Power Settings (Optional but Recommended):
     → After Power Loss: Power On
     → (Automatically restart after power outage)
   ```

3. **Save & Exit:** Press F10 → Yes

### Step 3: Install Proxmox VE

**Boot from USB and follow installer:**

1. **Proxmox VE Boot Menu:**
   - Select: **Install Proxmox VE (Graphical)**
   - Press Enter

2. **EULA:**
   - Click: **I agree**

3. **Target Harddisk:**
   ```
   Target Disk:    Your 500GB NVMe SSD
   Options:
     Filesystem:   ext4 (reliable, recommended)
     hdsize:       500 (use full disk)
     swapsize:     8 (8GB swap)
   ```
   - Click: **OK**

4. **Location and Timezone:**
   ```
   Country:        Germany (or your location)
   Timezone:       Europe/Berlin
   Keyboard:       German / English (your preference)
   ```

5. **Administration Password:**
   ```
   Password:       [Choose a STRONG password]
   Confirm:        [Re-enter password]
   Email:          your-email@example.com
   ```
   - **Important:** Remember this password! You'll need it for root login.

6. **Network Configuration:**
   ```
   Management Interface:   nic0 (or enp1s0 - your ethernet)
   Hostname (FQDN):        pve.homelab.local
   IP Address:             192.168.178.33 (choose unused IP)
   Netmask:                255.255.255.0
   Gateway:                192.168.178.1 (your router)
   DNS Server:             192.168.178.1
   ```
   
   **Note:** Adjust IP addresses to match YOUR network configuration.

7. **Install:**
   - Review settings
   - Click **Install**
   - Wait 10-15 minutes for installation

8. **Complete:**
   ```
   Installation successful!
   → Remove USB flash drive
   → Press ENTER to reboot
   ```

---

## Post-Installation Configuration

### First Login

After reboot, you'll see the Proxmox console:

```
Welcome to Proxmox Virtual Environment

pve login: root
Password: [your password]
```

### Step 1: Verify Network Connectivity

```bash
# Check IP configuration
ip addr show

# Test internet connectivity
ping -c 4 8.8.8.8

# Test DNS resolution
ping -c 4 google.com
```

**All pings should succeed!** If not, see [Troubleshooting](#troubleshooting).

### Step 2: Update Proxmox Repositories

**Disable Enterprise Repositories (no subscription):**

```bash
# Disable enterprise repo
echo "# deb https://enterprise.proxmox.com/debian/pve bookworm pve-enterprise" > /etc/apt/sources.list.d/pve-enterprise.list

# Add free community repo
echo "deb http://download.proxmox.com/debian/pve bookworm pve-no-subscription" > /etc/apt/sources.list.d/pve-no-subscription.list

# Update package lists
apt update

# Upgrade system (optional but recommended)
apt upgrade -y
```

### Step 3: Verify SSH Access

SSH should be enabled by default. Test from another computer:

```bash
# From your Windows laptop or another machine:
ssh root@192.168.178.33

# Enter your root password when prompted
```

**Success!** You can now manage Proxmox remotely without monitor/keyboard.

---

## Network Configuration

### Understanding Proxmox Networking

Proxmox creates a **Linux Bridge (vmbr0)** during installation:

```
Physical Network:
  Router (192.168.178.1)
    ↓ Ethernet
  nic0 (physical interface)
    ↓
  vmbr0 (Linux bridge - virtual switch)
    ↓
  VMs get IPs from bridge (can access network directly)
```

### Check Network Configuration

```bash
# View network configuration
cat /etc/network/interfaces
```

**Example configuration:**

```bash
# Bring up the loopback interface automatically at boot
auto lo

# Configure the loopback interface using IPv4
iface lo inet loopback
# 'lo' is the local loopback interface (127.0.0.1),
# used for internal communication within the system


# -------------------------------
# Physical Network Interface
# -------------------------------

# Define the physical NIC without assigning it an IP address
iface nic0 inet manual
# 'manual' means:
# - No IP configuration is applied directly
# - The interface is controlled by another device (bridge)
# In Proxmox, the physical NIC is typically attached to a bridge


# -------------------------------
# Bridge Interface for VMs
# -------------------------------

# Bring up the bridge automatically at boot
auto vmbr0

# Configure the bridge with a static IPv4 address
iface vmbr0 inet static

    # Static IP address assigned to the Proxmox host
    address 192.168.178.33/24
    # /24 means subnet mask 255.255.255.0

    # Default gateway for outbound traffic
    gateway 192.168.178.1
    # All traffic outside the local network is routed through this IP

    # Attach the physical NIC to the bridge
    bridge-ports nic0
    # This makes vmbr0 act like a virtual switch,
    # allowing VMs and containers to share the physical NIC

    # Disable Spanning Tree Protocol (STP)
    bridge-stp off
    # STP is unnecessary in most single-host homelab setups
    # and disabling it avoids startup delays

    # Set bridge forwarding delay to 0 seconds
    bridge-fd 0
    # Prevents delay when the bridge comes up,
    # useful for faster VM networking availability

    # DNS servers used by the Proxmox host
    dns-nameservers 192.168.178.1 8.8.8.8
    # First: local router DNS
    # Second: Google public DNS as fallback


# -------------------------------
# Include Additional Interface Configs
# -------------------------------

# Load additional network interface configurations
source /etc/network/interfaces.d/*
# Allows modular network configuration
# Proxmox and other tools may drop extra configs here

```

### Verify Network Status

```bash
# Check interfaces
ip link show

# Check bridge
brctl show

# Check routing
ip route show
```

---

## Important Note: WiFi on Proxmox

### ⚠️ WiFi is NOT Recommended for Proxmox Hosts

**Why WiFi Should NOT Be Used for Proxmox:**

1. **Bridge Incompatibility:**
   - WiFi adapters don't support traditional bridging
   - VMs cannot get direct network access via WiFi bridge
   - Requires complex NAT/routing workarounds

2. **Performance Issues:**
   - WiFi latency: 5-20ms (vs Ethernet <1ms)
   - Inconsistent throughput
   - Packet loss under load
   - Poor for VM disk I/O and storage traffic

3. **Proxmox Cluster Problems:**
   - Corosync (cluster communication) requires stable, low-latency network
   - WiFi dropouts cause cluster split-brain scenarios
   - **Not officially supported** by Proxmox

4. **Kubernetes-Specific Issues:**
   - etcd (Kubernetes control plane) is extremely latency-sensitive
   - Pod-to-pod networking requires stable connections
   - Container image pulls and registry traffic affected
   - Random timeouts and failures

### Firmware Challenges (Beelink SER5 Pro)

The Beelink SER5 Pro uses a **MediaTek MT7921 WiFi card**, which has firmware loading issues on Proxmox/Debian:

- Firmware files may not be included in default packages
- Driver compatibility issues with certain kernel versions
- Error -2 (file not found) or Error -40 (invalid format) common

**Attempted solutions that may or may not work:**
- Installing `firmware-mediatek` package
- Downloading firmware from linux-firmware repository
- Kernel updates may resolve in future Proxmox releases

**Recommendation:** Don't waste time troubleshooting WiFi for Proxmox hosts. Keep Proxmox on Ethernet

---

## Accessing Proxmox Remotely

### Web UI Access

**From any device on your network:**

1. Open web browser (Chrome, Firefox, Edge, Safari)
2. Navigate to: `https://192.168.178.33:8006`
3. Accept security warning (self-signed certificate)
4. Login:
   ```
   Username: root
   Password: [your Proxmox password]
   Realm:    Linux PAM standard authentication
   ```

**Web UI Features:**
- Create/manage VMs and containers
- Monitor resource usage
- Access VM consoles
- Configure storage
- Manage backups and snapshots
- Built-in terminal (Shell button)

### SSH Access

**From Windows PowerShell/CMD:**

```bash
ssh root@192.168.178.33
```

**From Linux/Mac Terminal:**

```bash
ssh root@192.168.178.33
```

**From Mobile (Samsung Tab, iPhone, Android):**

Install **JuiceSSH** (Android) or **Termius** (iOS):

```
Connection Details:
  Host:     192.168.178.33
  Port:     22
  Username: root
  Password: [your password]
```
---

## Next Steps

### After Proxmox is Installed

Your Proxmox server is now ready! Next steps for building your Kubernetes homelab:

1. **Upload Ubuntu Server ISO:**
   - Download Ubuntu Server 24.04 LTS
   - Upload to Proxmox via Web UI: Datacenter → Storage → local → ISO Images

2. **Create VM Template:**
   - Create Ubuntu Server 24.04 VM
   - Configure cloud-init
   - Install base packages
   - Convert to template

3. **Clone VMs for Kubernetes:**
   - 1x Control Plane VM
   - 2-3x Worker Node VMs

4. **Install Kubernetes Cluster:**
   - Use kubeadm (CKA exam requirement)
   - Install containerd runtime
   - Deploy Calico CNI
   - Configure NGINX Ingress

5. **Add Kubernetes Tools:**
   - Prometheus + Grafana monitoring
   - ArgoCD for GitOps
   - Kubernetes Dashboard

6. **Practice CKA Scenarios:**
   - Cluster upgrades
   - etcd backup/restore
   - Network policy configuration
   - Troubleshooting exercises

---

## Troubleshooting

### Network Issues

**Problem: Can't ping router or internet**

```bash
# Check interface status
ip link show

# Check IP address
ip addr show vmbr0

# Check routing table
ip route show

# Bring interface up if down
ip link set vmbr0 up

# Request DHCP (if using DHCP)
dhclient vmbr0

# Or set static IP manually
ip addr add 192.168.178.33/24 dev vmbr0
ip route add default via 192.168.178.1
```

**Problem: Wrong network configuration during install**

Edit `/etc/network/interfaces`:

```bash
nano /etc/network/interfaces
```

Update settings, then:

```bash
systemctl restart networking
# Or reboot
reboot
```

### Repository Errors (401 Unauthorized)

If you see errors about `enterprise.proxmox.com`:

```bash
# Disable enterprise repos (already shown in post-install section)
echo "# deb https://enterprise.proxmox.com/debian/pve bookworm pve-enterprise" > /etc/apt/sources.list.d/pve-enterprise.list

apt update
```

This is normal for non-subscription users.

### Can't Access Web UI

**Check from Proxmox console:**

```bash
# Verify web server is running
systemctl status pveproxy

# Check if port 8006 is listening
netstat -tulpn | grep 8006

# Restart web interface if needed
systemctl restart pveproxy pvedaemon
```

**Check firewall on your laptop:**
- Ensure Windows Firewall isn't blocking HTTPS
- Try from a different browser
- Try incognito/private mode

### Virtualization Not Working

**Error: "KVM virtualization not available"**

```bash
# Check if CPU supports virtualization
egrep -c '(vmx|svm)' /proc/cpuinfo

# Should return number > 0 (16 for Ryzen 7 5800H)

# Check if modules are loaded
lsmod | grep kvm

# Load modules if missing
modprobe kvm_amd  # For AMD CPUs
```

If still not working, check BIOS virtualization settings.

### System Performance Issues

**Monitor resource usage:**

```bash
# CPU and memory
htop

# Disk I/O
iostat

# Network
iftop

# Overall system
top
```

---

## Conclusion

You now have a fully functional Proxmox VE server running on your Beelink SER5 Pro mini PC! This provides a solid foundation for:

- ✅ Running multiple virtual machines efficiently
- ✅ Building a multi-node Kubernetes cluster
- ✅ Practicing for CKA certification
- ✅ Learning enterprise infrastructure tools

### Key Takeaways

1. **Proxmox is production-grade:** Professional hypervisor suitable for enterprise learning
2. **Ethernet is essential:** Don't use WiFi for Proxmox hosts
3. **Remote management works great:** No monitor needed after initial setup
4. **Resource planning matters:** 32GB RAM supports 3-4 Kubernetes VMs comfortably
5. **Cost-effective:** ~€350 hardware investment, ~€10/month electricity

### Resources

- **Proxmox Official Documentation:** [https://pve.proxmox.com/pve-docs/](https://pve.proxmox.com/pve-docs/)
- **Proxmox Forums:** [https://forum.proxmox.com/](https://forum.proxmox.com/)
- **CKA Exam Info:** [https://www.cncf.io/training/certification/cka/](https://www.cncf.io/training/certification/cka/)
- **Kubernetes Documentation:** [https://kubernetes.io/docs/](https://kubernetes.io/docs/)

### What's Next?

Continue with the next guide: **"Building a Multi-Node Kubernetes Cluster on Proxmox for CKA Preparation"**

---

## Changelog

- **2025-12-25:** Initial publication - Proxmox installation on Beelink SER5 Pro
- **Future:** Will add Kubernetes cluster deployment guide

---

**Questions or Issues?** Feel free to open an issue on this repository or connect with me!

**Happy Homelabbing! 🚀**
