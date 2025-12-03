# Week 8 Quick Reference - Netplan & Linux Networking

Quick command reference for Netplan configuration, network troubleshooting, and Linux networking commands.

---

## Netplan Commands

| Task | Command | Example |
|------|---------|---------|
| View Netplan config | `sudo cat /etc/netplan/*.yaml` | `sudo cat /etc/netplan/01-netcfg.yaml` |
| List Netplan files | `ls -la /etc/netplan/` | `ls -la /etc/netplan/` |
| Test Netplan config | `sudo netplan try` | `sudo netplan try` |
| Apply Netplan config | `sudo netplan apply` | `sudo netplan apply` |
| Generate config | `sudo netplan generate` | `sudo netplan generate` |
| Debug Netplan | `sudo netplan --debug generate` | `sudo netplan --debug generate` |
| Validate Netplan | `sudo netplan --debug apply` | `sudo netplan --debug apply` |

---

## Network Interface Commands

| Task | Command | Example |
|------|---------|---------|
| View all interfaces | `ip a` | `ip a` |
| View IPv4 only | `ip -4 a` | `ip -4 a` |
| View specific interface | `ip a show enp0s3` | `ip a show enp0s3` |
| View interface status | `ip link show` | `ip link show` |
| Bring interface up | `sudo ip link set enp0s3 up` | `sudo ip link set enp0s3 up` |
| Bring interface down | `sudo ip link set enp0s3 down` | `sudo ip link set enp0s3 down` |
| View MAC address | `ip link show enp0s3` | `ip link show enp0s3` |
| Get IP address | `hostname -I` | `hostname -I` |
| Get hostname | `hostname` | `hostname` |

---

## Routing Commands

| Task | Command | Example |
|------|---------|---------|
| View routing table | `ip r` | `ip r` |
| View default route | `ip r | grep default` | `ip r | grep default` |
| View route for network | `ip r show 192.168.1.0/24` | `ip r show 192.168.1.0/24` |
| Add route | `sudo ip route add 192.168.2.0/24 via 192.168.1.1` | `sudo ip route add 192.168.2.0/24 via 192.168.1.1` |
| Delete route | `sudo ip route del 192.168.2.0/24` | `sudo ip route del 192.168.2.0/24` |
| Flush routing table | `sudo ip route flush` | `sudo ip route flush` |

---

## DNS Commands

| Task | Command | Example |
|------|---------|---------|
| View DNS status | `resolvectl status` | `resolvectl status` |
| View DNS config | `cat /run/systemd/resolve/resolv.conf` | `cat /run/systemd/resolve/resolv.conf` |
| Test DNS resolution | `nslookup google.com` | `nslookup google.com` |
| Test DNS with dig | `dig google.com` | `dig google.com` |
| Flush DNS cache | `sudo systemd-resolve --flush-caches` | `sudo systemd-resolve --flush-caches` |
| Restart DNS service | `sudo systemctl restart systemd-resolved` | `sudo systemctl restart systemd-resolved` |
| View DNS statistics | `resolvectl statistics` | `resolvectl statistics` |

---

## Network Service Management

| Task | Command | Example |
|------|---------|---------|
| Check NetworkManager status | `systemctl status NetworkManager` | `systemctl status NetworkManager` |
| Start NetworkManager | `sudo systemctl start NetworkManager` | `sudo systemctl start NetworkManager` |
| Stop NetworkManager | `sudo systemctl stop NetworkManager` | `sudo systemctl stop NetworkManager` |
| Restart NetworkManager | `sudo systemctl restart NetworkManager` | `sudo systemctl restart NetworkManager` |
| Enable NetworkManager | `sudo systemctl enable NetworkManager` | `sudo systemctl enable NetworkManager` |
| Check systemd-resolved | `systemctl status systemd-resolved` | `systemctl status systemd-resolved` |
| Restart systemd-resolved | `sudo systemctl restart systemd-resolved` | `sudo systemctl restart systemd-resolved` |

---

## Connectivity Testing Commands

| Task | Command | Example |
|------|---------|---------|
| Ping host | `ping -c 4 8.8.8.8` | `ping -c 4 8.8.8.8` |
| Ping with count | `ping -c 10 google.com` | `ping -c 10 google.com` |
| Continuous ping | `ping 8.8.8.8` | `ping 8.8.8.8` (Ctrl+C to stop) |
| Test specific port | `nc -zv hostname 80` | `nc -zv google.com 80` |
| Trace route | `traceroute google.com` | `traceroute google.com` |
| Test HTTP connection | `curl -I http://google.com` | `curl -I http://google.com` |

---

## Network Logs and Diagnostics

| Task | Command | Example |
|------|---------|---------|
| View NetworkManager logs | `journalctl -u NetworkManager` | `journalctl -u NetworkManager` |
| View recent logs | `journalctl -u NetworkManager -n 50` | `journalctl -u NetworkManager -n 50` |
| View logs since boot | `journalctl -u NetworkManager -b` | `journalctl -u NetworkManager -b` |
| Follow logs | `journalctl -u NetworkManager -f` | `journalctl -u NetworkManager -f` |
| View system logs | `journalctl -xe` | `journalctl -xe` |
| Check network errors | `dmesg | grep -i network` | `dmesg | grep -i network` |

---

## Netplan Configuration Examples

### DHCP Configuration

```yaml
network:
    version: 2
    renderer: NetworkManager
    ethernets:
        enp0s3:
            dhcp4: true
```

### Static IP Configuration

```yaml
network:
    version: 2
    renderer: NetworkManager
    ethernets:
        enp0s3:
            dhcp4: false
            addresses:
                - 192.168.1.100/24
            routes:
                - to: default
                  via: 192.168.1.1
            nameservers:
                addresses:
                    - 8.8.8.8
                    - 8.8.4.4
```

### Multiple DNS Servers

```yaml
network:
    version: 2
    renderer: NetworkManager
    ethernets:
        enp0s3:
            dhcp4: true
            nameservers:
                addresses:
                    - 8.8.8.8
                    - 8.8.4.4
                    - 1.1.1.1
```

### NetworkManager Only (Desktop Default)

```yaml
network:
    version: 2
    renderer: NetworkManager
```

---

## Common Task Combinations

### Complete Network Check

```bash
# View IP address
ip a

# View routing
ip r

# Check DNS
resolvectl status

# Test connectivity
ping -c 4 8.8.8.8

# Test DNS resolution
nslookup google.com
```

### Configure DHCP Network

```bash
# Backup existing config
sudo cp /etc/netplan/*.yaml /etc/netplan/backup.yaml

# Edit configuration
sudo nano /etc/netplan/01-netcfg.yaml

# Test configuration
sudo netplan try

# Apply configuration
sudo netplan apply

# Verify
ip a
```

### Configure Static IP

```bash
# Find interface name
ip a

# Backup config
sudo cp /etc/netplan/*.yaml /etc/netplan/backup.yaml

# Edit configuration
sudo nano /etc/netplan/01-netcfg.yaml

# Test and apply
sudo netplan try
sudo netplan apply

# Verify
ip a
ip r
```

### Troubleshoot Network Issues

```bash
# Check interface status
ip link show

# Check IP address
ip a

# Check routing
ip r

# Check DNS
resolvectl status

# Check NetworkManager
systemctl status NetworkManager

# View logs
journalctl -u NetworkManager -n 50

# Restart NetworkManager
sudo systemctl restart NetworkManager
```

---

## File Locations Reference

| Purpose | File/Directory |
|---------|---------------|
| Netplan config | `/etc/netplan/*.yaml` |
| NetworkManager profiles | `/etc/NetworkManager/system-connections/` |
| DNS config | `/run/systemd/resolve/resolv.conf` |
| Network logs | `journalctl -u NetworkManager` |
| System logs | `/var/log/syslog` |

---

## YAML Syntax Rules

### Key Rules:
- **Indentation:** Use spaces (2 or 4), NOT tabs
- **Colons:** Must have space after colon (`key: value`)
- **Lists:** Use dashes (`- item1`)
- **Nested:** Indent child elements
- **Case sensitive:** Use lowercase for keys

### Common Mistakes:
- ❌ Using tabs instead of spaces
- ❌ Missing colons after keys
- ❌ Wrong indentation level
- ❌ Incorrect interface name

---

## Troubleshooting Commands

| Problem | Diagnostic Command | Fix Command |
|---------|-------------------|-------------|
| No IP address | `ip a` | `sudo netplan apply` |
| DNS not working | `resolvectl status` | `sudo systemctl restart systemd-resolved` |
| Interface down | `ip link show` | `sudo ip link set enp0s3 up` |
| YAML error | `sudo netplan --debug generate` | Fix YAML syntax |
| NetworkManager down | `systemctl status NetworkManager` | `sudo systemctl restart NetworkManager` |
| Can't connect | `ping 8.8.8.8` | Check gateway: `ip r` |

---

## VirtualBox Network Modes

### NAT Mode (Default)
- **Use:** DHCP only
- **Static IP:** ❌ Not supported
- **Host access:** Limited
- **Internet:** ✅ Yes

### Bridged Mode
- **Use:** DHCP or Static IP
- **Static IP:** ✅ Supported
- **Host access:** ✅ Full access
- **Internet:** ✅ Yes

**Check mode in VirtualBox:**
- Settings → Network → Adapter → Attached to

---

## Quick Diagnostic Checklist

```bash
# 1. Check interface
ip a

# 2. Check routing
ip r

# 3. Check DNS
resolvectl status

# 4. Test connectivity
ping -c 4 8.8.8.8

# 5. Test DNS
nslookup google.com

# 6. Check services
systemctl status NetworkManager
systemctl status systemd-resolved

# 7. View logs
journalctl -u NetworkManager -n 20
```

---

## Emergency Recovery Commands

**If network is broken:**

```bash
# Restore from backup
sudo cp /etc/netplan/backup.yaml /etc/netplan/01-netcfg.yaml

# Apply backup
sudo netplan apply

# Or restart NetworkManager
sudo systemctl restart NetworkManager

# Or reboot (last resort)
sudo reboot
```

**Reset to DHCP:**

```bash
# Edit Netplan config
sudo nano /etc/netplan/01-netcfg.yaml

# Set dhcp4: true
# Save and apply
sudo netplan apply
```

---

## 💡 Tips & Tricks

### Command Line Shortcuts
- **Up Arrow** - Recall previous command
- **Tab** - Autocomplete file/folder names
- **Ctrl + C** - Stop current command
- **Ctrl + L** - Clear screen
- **Ctrl + R** - Search command history

### Useful Aliases
Add to `~/.bashrc`:
```bash
alias ipa='ip a'
alias ipr='ip r'
alias netplan-test='sudo netplan try'
alias netplan-apply='sudo netplan apply'
```

### View Configuration Safely
```bash
# View without editing
sudo cat /etc/netplan/*.yaml

# View with syntax highlighting (if vim installed)
sudo vim /etc/netplan/*.yaml
```

---

## 📝 Examples for This Week's Lab

### Activity 1: View Current Configuration

```bash
# View Netplan config
sudo cat /etc/netplan/*.yaml

# View network interfaces
ip a

# View routing
ip r

# View DNS
resolvectl status
```

### Activity 2: Check Network Status

```bash
# Check all network info
ip a
ip r
resolvectl status

# Test connectivity
ping -c 4 8.8.8.8
```

### Activity 3: Configure DHCP

```bash
# Backup
sudo cp /etc/netplan/*.yaml /etc/netplan/backup.yaml

# Edit (add DHCP config)
sudo nano /etc/netplan/01-netcfg.yaml

# Test
sudo netplan try

# Apply
sudo netplan apply

# Verify
ip a
```

### Activity 4: Configure Static IP (Bridged Mode Only)

```bash
# Find interface
ip a

# Backup
sudo cp /etc/netplan/*.yaml /etc/netplan/backup.yaml

# Edit (add static IP config)
sudo nano /etc/netplan/01-netcfg.yaml

# Test
sudo netplan try

# Apply
sudo netplan apply

# Verify
ip a
ip r
ping -c 4 192.168.1.1
```

### Activity 5: Troubleshoot Issues

```bash
# Check everything
ip a
ip r
resolvectl status
systemctl status NetworkManager

# View logs
journalctl -u NetworkManager -n 50

# Restart services
sudo systemctl restart NetworkManager
sudo systemctl restart systemd-resolved

# Test
ping -c 4 8.8.8.8
nslookup google.com
```

---

## 📌 Remember!

- **Always backup** before making changes: `sudo cp /etc/netplan/*.yaml /etc/netplan/backup.yaml`
- **Use `netplan try`** before `netplan apply` to test safely
- **VirtualBox NAT mode** = DHCP only (static IP won't work)
- **Bridged mode** = DHCP or Static IP
- **Check interface name** with `ip a` before configuring
- **Use spaces, not tabs** for YAML indentation
- **NetworkManager** is renderer for Desktop Ubuntu
- **systemd-networkd** is renderer for Server Ubuntu

---

**💾 Save this page!** You'll use these commands throughout the course!

**🔖 Pro tip:** Print this page or keep it open on a second screen during labs!
