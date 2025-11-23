# Week 7 Quick Reference - Linux System Operations and Troubleshooting

Quick command reference for Linux filesystem, processes, services, logs, packages, and networking.

---

## Filesystem Navigation Commands

### Explore Root Directory

```bash
ls /
```

Lists contents of root directory (top-level).

```bash
ls -l /
```

Lists root directory with detailed information.

### Navigate Key Directories

```bash
cd /etc          # Configuration files
cd /var/log      # Log files
cd /usr/bin      # User applications
cd /home         # User directories
cd /bin          # Essential commands
```

### View Configuration Files

```bash
cat /etc/passwd          # User accounts
cat /etc/group           # Groups
cat /etc/hosts           # Hostname to IP mappings
cat /etc/hostname        # System hostname
```

### View Log Files

```bash
ls /var/log              # List all log files
sudo tail -50 /var/log/syslog    # View system log
sudo tail -20 /var/log/auth.log  # View authentication log
sudo tail -10 /var/log/dpkg.log  # View package log
```

---

## Process Management Commands

### View Processes

```bash
ps
```

Shows processes for current user.

```bash
ps aux
```

Shows all processes with detailed information.

```bash
ps aux | grep processname
```

Shows processes matching a name.

### Real-Time Process Viewer

```bash
top
```

Shows live process information (press `q` to quit).

```bash
htop
```

Improved process viewer (if installed - `sudo apt install htop`).

### Kill Processes

```bash
kill PID
```

Terminates a process gracefully (replace PID with process ID).

```bash
kill -9 PID
```

Forces immediate termination (use only if needed).

```bash
killall processname
```

Kills all processes with that name.

### Find Process ID

```bash
ps aux | grep firefox
```

Finds Firefox process and shows its PID.

```bash
pgrep firefox
```

Shows PIDs of processes matching name.

---

## Service Management Commands (systemctl)

### View Services

```bash
systemctl list-units --type=service
```

Lists all system services.

```bash
systemctl status servicename
```

Shows detailed status of a service.

```bash
systemctl list-unit-files --type=service
```

Lists all service files.

### Control Services

```bash
sudo systemctl start servicename
```

Starts a service.

```bash
sudo systemctl stop servicename
```

Stops a service.

```bash
sudo systemctl restart servicename
```

Restarts a service.

```bash
sudo systemctl reload servicename
```

Reloads service configuration (without stopping).

### Enable/Disable Services

```bash
sudo systemctl enable servicename
```

Enables service to start on boot.

```bash
sudo systemctl disable servicename
```

Disables service from starting on boot.

```bash
sudo systemctl is-enabled servicename
```

Checks if service is enabled.

```bash
sudo systemctl is-active servicename
```

Checks if service is currently running.

---

## Log File Commands

### View System Logs

```bash
sudo tail -50 /var/log/syslog
```

Shows last 50 lines of system log.

```bash
sudo tail -f /var/log/syslog
```

Follows system log in real-time (press `Ctrl + C` to stop).

```bash
sudo less /var/log/syslog
```

Views system log page by page (press `q` to quit).

### View Authentication Logs

```bash
sudo tail -20 /var/log/auth.log
```

Shows recent authentication events.

```bash
sudo grep "Failed password" /var/log/auth.log
```

Shows failed login attempts.

### Search Logs

```bash
sudo grep -i "error" /var/log/syslog
```

Searches for "error" in system log (case-insensitive).

```bash
sudo grep "keyword" /var/log/syslog | tail -10
```

Searches and shows last 10 matches.

---

## System Journal Commands (journalctl)

### View Journal

```bash
sudo journalctl
```

Shows all journal entries (press `q` to quit, use arrow keys to scroll).

```bash
sudo journalctl -n 50
```

Shows last 50 journal entries.

```bash
sudo journalctl -f
```

Follows journal in real-time (press `Ctrl + C` to stop).

### Filter Journal

```bash
sudo journalctl -u servicename
```

Shows logs for a specific service.

```bash
sudo journalctl --since today
```

Shows logs from today.

```bash
sudo journalctl --since "2024-11-23 10:00:00" --until "2024-11-23 12:00:00"
```

Shows logs from specific time range.

```bash
sudo journalctl -p err
```

Shows only error level messages.

```bash
sudo journalctl -p warning
```

Shows warning and error messages.

### Journal Priority Levels

- `emerg` = Emergency
- `alert` = Alert
- `crit` = Critical
- `err` = Error
- `warning` = Warning
- `notice` = Notice
- `info` = Information
- `debug` = Debug

---

## Package Management Commands (apt)

### Update Package List

```bash
sudo apt update
```

Updates list of available packages (doesn't install anything).

### Upgrade Packages

```bash
sudo apt upgrade
```

Upgrades all installed packages to latest versions.

```bash
sudo apt update && sudo apt upgrade -y
```

Updates and upgrades (the `-y` auto-confirms).

### Search Packages

```bash
apt search keyword
```

Searches for packages matching keyword.

```bash
apt show packagename
```

Shows detailed information about a package.

### Install Packages

```bash
sudo apt install packagename
```

Installs a package.

```bash
sudo apt install package1 package2 package3
```

Installs multiple packages.

### Remove Packages

```bash
sudo apt remove packagename
```

Removes a package (keeps configuration files).

```bash
sudo apt purge packagename
```

Removes package and configuration files.

```bash
sudo apt autoremove
```

Removes packages that are no longer needed.

```bash
sudo apt autoclean
```

Removes old package files from cache.

### List Packages

```bash
apt list --installed
```

Lists all installed packages.

```bash
apt list --upgradable
```

Lists packages that can be upgraded.

---

## Network Commands

### View Network Interfaces

```bash
ip a
```

Shows all network interfaces with IP addresses.

```bash
ip addr show
```

Same as `ip a` (more explicit).

```bash
ifconfig
```

Shows network interfaces (older command, may need `sudo apt install net-tools`).

### View IP Address

```bash
hostname -I
```

Shows IP addresses of the system.

```bash
ip a | grep inet
```

Shows only IP address lines.

### View Routing Table

```bash
ip r
```

Shows routing table.

```bash
ip route show
```

Same as `ip r` (more explicit).

```bash
route -n
```

Shows routing table (older command, needs `net-tools`).

### Test Network Connectivity

```bash
ping -c 4 127.0.0.1
```

Pings localhost (4 packets).

```bash
ping -c 4 192.168.1.1
```

Pings default gateway.

```bash
ping -c 4 8.8.8.8
```

Pings Google DNS (tests internet connectivity).

```bash
ping -c 4 google.com
```

Pings by hostname (tests DNS and internet).

```bash
ping hostname
```

Continuous ping (press `Ctrl + C` to stop).

### DNS Commands

```bash
nslookup google.com
```

Looks up IP address for a hostname.

```bash
dig google.com
```

DNS lookup tool (more detailed).

```bash
host google.com
```

Simple DNS lookup.

### Network Statistics

```bash
netstat -tuln
```

Shows network connections and listening ports.

```bash
ss -tuln
```

Modern replacement for netstat.

---

## System Information Commands

### System Information

```bash
uname -a
```

Shows system information (kernel version, etc.).

```bash
hostname
```

Shows system hostname.

```bash
uptime
```

Shows how long system has been running and load average.

```bash
whoami
```

Shows current username.

```bash
id
```

Shows current user's UID, GID, and groups.

### Disk Usage

```bash
df -h
```

Shows disk space usage (human-readable).

```bash
du -h foldername
```

Shows disk usage of a folder.

```bash
free -h
```

Shows memory (RAM) usage.

---

## Filesystem Structure Reference

### Key Directories

| Directory | Purpose | Examples |
|-----------|---------|----------|
| `/` | Root directory | Top of filesystem tree |
| `/etc` | Configuration files | `/etc/passwd`, `/etc/hosts`, `/etc/ssh/` |
| `/bin` | Essential commands | `ls`, `cp`, `mv`, `rm` |
| `/sbin` | System admin commands | `ifconfig`, `fdisk` (need root) |
| `/usr` | User applications | `/usr/bin/`, `/usr/lib/`, `/usr/share/` |
| `/var` | Variable data | `/var/log/`, `/var/spool/`, `/var/tmp/` |
| `/home` | User directories | `/home/student/`, `/home/admin/` |
| `/tmp` | Temporary files | Cleared on reboot |
| `/dev` | Device files | Hardware devices |
| `/proc` | Process information | Virtual filesystem |
| `/sys` | System information | Virtual filesystem |
| `/boot` | Boot files | Kernel, bootloader |
| `/opt` | Optional software | Third-party applications |
| `/root` | Root user's home | Administrator's files |

### Common Configuration Files

| File | Purpose |
|------|---------|
| `/etc/passwd` | User accounts |
| `/etc/group` | Groups |
| `/etc/hosts` | Hostname to IP mappings |
| `/etc/hostname` | System hostname |
| `/etc/resolv.conf` | DNS servers |
| `/etc/fstab` | Filesystem mount table |
| `/etc/ssh/sshd_config` | SSH server configuration |

### Common Log Files

| File | Purpose |
|------|---------|
| `/var/log/syslog` | General system messages |
| `/var/log/auth.log` | Authentication and login attempts |
| `/var/log/dpkg.log` | Package installation log |
| `/var/log/kern.log` | Kernel messages |
| `/var/log/boot.log` | Boot messages |
| `/var/log/apache2/` | Web server logs (if installed) |

---

## Process Status Codes

When viewing `ps aux`, the STAT column shows process status:

| Code | Meaning |
|------|---------|
| R | Running |
| S | Sleeping (waiting for event) |
| D | Uninterruptible sleep (usually I/O) |
| Z | Zombie (terminated but not reaped) |
| T | Stopped (by job control signal) |
| t | Stopped by debugger |
| W | Paging (not valid since kernel 2.6) |
| X | Dead (should never be seen) |
| < | High priority |
| N | Low priority |
| L | Pages locked in memory |
| s | Session leader |
| l | Multi-threaded |
| + | Foreground process group |

---

## Service Status Meanings

When viewing `systemctl status`:

| Status | Meaning |
|--------|---------|
| `loaded` | Service configuration loaded |
| `active (running)` | Service is currently running |
| `active (exited)` | Service completed successfully |
| `inactive (dead)` | Service is stopped |
| `enabled` | Service will start on boot |
| `disabled` | Service will NOT start on boot |
| `failed` | Service failed to start |

---

## Network Troubleshooting Sequence

### Step 1: Check Interface

```bash
ip a
```

Verify network interface is UP and has an IP address.

### Step 2: Check Routing

```bash
ip r
```

Verify default gateway is configured.

### Step 3: Test Local

```bash
ping -c 4 127.0.0.1
```

Test if network stack works.

### Step 4: Test Gateway

```bash
ping -c 4 192.168.1.1
```

Test if you can reach router (use your gateway IP).

### Step 5: Test DNS

```bash
ping -c 4 8.8.8.8
```

Test internet connectivity (bypasses DNS).

### Step 6: Test DNS Resolution

```bash
ping -c 4 google.com
```

Test if DNS is working.

---

## Common Troubleshooting Commands

### Check Service Status

```bash
systemctl status servicename
sudo journalctl -u servicename
```

### Check Network Issues

```bash
ip a                    # Check interfaces
ip r                    # Check routing
ping -c 4 gateway       # Test connectivity
sudo journalctl -u NetworkManager
```

### Check Disk Space

```bash
df -h                   # Check disk usage
du -h /path             # Check folder size
```

### Check Memory

```bash
free -h                 # Check RAM usage
top                     # See what's using memory
```

### Check System Load

```bash
uptime                  # Shows load average
top                     # Shows current load
```

---

## Best Practices

1. **Always update before installing:**
   ```bash
   sudo apt update && sudo apt upgrade
   ```

2. **Check logs when troubleshooting:**
   ```bash
   sudo journalctl -u servicename
   sudo tail -50 /var/log/syslog
   ```

3. **Use `kill` before `kill -9`:**
   - Try graceful termination first
   - Only use `-9` if necessary

4. **Verify before modifying:**
   - Check service status before stopping
   - View logs before making changes
   - Understand what you're doing

5. **Document your changes:**
   - Note what services you modify
   - Keep track of package installations
   - Document network configuration changes

---

## For Your PLR

Include these elements:

**Technical Evidence:**
- Screenshots of `ls /` showing filesystem structure
- Screenshots of `ps aux` showing processes
- Screenshots of `systemctl status` showing services
- Screenshots of `journalctl` showing logs
- Screenshots of `ip a` and `ip r` showing network config
- Screenshots of `apt` commands showing package management

**Explanation:**
- How Linux filesystem is organized
- How processes and services work
- How to use logs for troubleshooting
- How package management works
- How network configuration is viewed

**Reflection:**
- Challenges with system administration
- How you used logs to troubleshoot
- What you learned about Linux system operations
- Real-world applications

---

**Quick access:** Keep this page open during your lab for fast command reference!

