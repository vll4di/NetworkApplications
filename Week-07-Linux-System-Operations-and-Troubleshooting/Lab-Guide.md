# Week 7 Lab Guide: Linux System Operations and Troubleshooting

Complete guide showing both GUI (Graphical User Interface) and CLI (Command Line Interface) methods.

---

## Lab Overview

### What is Week 7 About:
**Exploring Linux System Operations and Troubleshooting Fundamentals**

### PC Requirements:
- **Ubuntu Linux** (Virtual Machine, lab PC, or LOD platform)
- Terminal/Shell access
- Administrator/sudo privileges
- Basic familiarity with CLI commands from Weeks 5 and 6

### What You'll Do:
- **Activity 1:** Navigate and understand the Linux filesystem structure (/etc, /bin, /usr, /var, /home)
- **Activity 2:** Manage processes and system services using ps, top, systemctl, and kill
- **Activity 3:** Use log files and system journal for troubleshooting (journalctl, /var/log/syslog)
- **Activity 4:** Install, update and remove software packages using apt
- **Activity 5:** Understand basic IP concepts and view network settings (ip a, ip r, hostname -I, ping)

---

## Important Terms

| Term | Definition | Simple Explanation |
|------|------------|-------------------|
| Filesystem | The way files are organized on disk | The folder structure of Linux |
| Root Directory (/) | The top-level directory in Linux | Like C:\ in Windows, but everything starts from / |
| /etc | Directory containing system configuration files | Where system settings are stored |
| /bin | Directory containing essential system commands | Basic commands needed to boot the system |
| /usr | Directory containing user applications and programs | Where installed software lives |
| /var | Directory containing variable data (logs, spool files) | Where logs and temporary data are stored |
| /home | Directory containing user home folders | Where each user's personal files are stored |
| Process | A running program or application | A program that's currently executing |
| Service | A background program that runs automatically | Programs that start when system boots |
| systemd | System and service manager in modern Linux | Manages services and system startup |
| systemctl | Command to control systemd services | Tool to start, stop, and manage services |
| Log File | File that records system events and messages | History of what happened on the system |
| Package Manager | Tool to install, update, and remove software | Like an app store for Linux |
| apt | Advanced Package Tool - Ubuntu's package manager | Command-line tool to manage software |
| IP Address | Unique identifier for a device on a network | Like a house address, but for computers |
| Subnet Mask | Defines which part of IP is network and which is host | Determines network boundaries |
| Default Gateway | Router that connects to other networks | The "exit door" to the internet |
| DNS | Domain Name System - translates names to IPs | Converts google.com to 142.250.191.14 |

---

## Background Knowledge: Linux Filesystem Structure

**What is the Linux Filesystem?**
- Linux uses a single-root filesystem (everything starts from `/`)
- Unlike Windows which has C:, D: drives, Linux has one tree structure
- All directories branch from the root directory `/`

**Why This Matters:**
- Understanding the filesystem helps you find configuration files
- Logs are always in predictable locations
- System files are organized logically
- Essential for troubleshooting and system administration

**Key Directories:**
- `/etc` = Configuration files (system settings)
- `/bin` = Essential commands (basic tools)
- `/usr` = User applications (installed software)
- `/var` = Variable data (logs, temporary files)
- `/home` = User directories (personal files)

---

## SETUP: PREPARING FOR WEEK 7

**What we're doing:** We need to set up our Ubuntu environment for system operations and troubleshooting.

1. **Start your Ubuntu system**
   - Open your Ubuntu VM, lab PC, or LOD platform
   - Log in with your credentials

2. **Open Terminal**
   - Press **Ctrl + Alt + T**
   - *Terminal window opens*

3. **Verify you have sudo access**
   - Type: `sudo whoami`
   - Press Enter
   - *Enter your password when prompted*
   - *You should see: `root`*
   - *This confirms you have administrator privileges*

**What you should see:**
```
$ sudo whoami
[sudo] password for student: 
root
$
```

---

## ACTIVITY 1: UNDERSTANDING LINUX FILESYSTEM STRUCTURE

**Do on:** Ubuntu Linux system

**What we're doing:** We're exploring the Linux filesystem to understand where different types of files are stored. This is essential for system administration and troubleshooting.

**Goal:** Navigate and understand the Linux filesystem structure.

**Time:** 30 minutes

---

## TASK 1: EXPLORE ROOT DIRECTORY

**What this does:** We're looking at the top-level directory structure to see how Linux organizes files.

### CLI METHOD

1. **Open Terminal**
   - Press **Ctrl + Alt + T**
   - *Terminal window opens*

2. **List root directory contents**
   - Type: `ls /`
   - Press Enter
   - *You will see a list of directories*

**What you should see:**
```
$ ls /
bin   boot   dev   etc   home   lib   lib32   lib64   libx32   lost+found   media   mnt   opt   proc   root   run   sbin   snap   srv   sys   tmp   usr   var
$
```

**What these directories are:**
- `bin` = Essential binary files (commands)
- `boot` = Boot loader files
- `dev` = Device files (hardware)
- `etc` = Configuration files
- `home` = User home directories
- `usr` = User programs and data
- `var` = Variable data (logs, etc.)

3. **View detailed information**
   - Type: `ls -l /`
   - Press Enter
   - *You will see detailed information about each directory*

**What you should see:**
```
$ ls -l /
total 56
drwxr-xr-x   2 root root  4096 Nov 23 10:00 bin
drwxr-xr-x   3 root root  4096 Nov 23 10:00 boot
drwxr-xr-x  15 root root  3000 Nov 23 10:00 dev
drwxr-xr-x 150 root root 12288 Nov 23 10:00 etc
...
$
```

---

## TASK 2: EXPLORE /etc DIRECTORY (CONFIGURATION FILES)

**What this does:** We're exploring where system configuration files are stored. This is where you'll find settings for users, groups, networking, and services.

### CLI METHOD

1. **Navigate to /etc**
   - Type: `cd /etc`
   - Press Enter
   - *You're now in the /etc directory*

2. **List configuration files**
   - Type: `ls`
   - Press Enter
   - *You will see many configuration files and directories*

**What you should see:**
```
$ cd /etc
$ ls
adduser.conf          hosts              passwd
alternatives          hosts.allow        passwd-
apt                   hosts.deny         profile
...
$
```

3. **View important configuration files**

**View users file:**
   - Type: `cat /etc/passwd | head -5`
   - Press Enter
   - *You will see user account information*

**What you should see:**
```
$ cat /etc/passwd | head -5
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
$
```

**View groups file:**
   - Type: `cat /etc/group | head -5`
   - Press Enter
   - *You will see group information*

**View hosts file:**
   - Type: `cat /etc/hosts`
   - Press Enter
   - *You will see hostname to IP address mappings*

**What you should see:**
```
$ cat /etc/hosts
127.0.0.1       localhost
127.0.1.1       ubuntu24
$
```

4. **Explore /etc structure**
   - Type: `ls -d /etc/*/`
   - Press Enter
   - *You will see subdirectories in /etc*

**Common /etc subdirectories:**
- `/etc/ssh/` = SSH configuration
- `/etc/apt/` = Package manager configuration
- `/etc/systemd/` = Service configuration
- `/etc/netplan/` = Network configuration (Week 8)

---

## TASK 3: EXPLORE /var/log DIRECTORY (LOG FILES)

**What this does:** We're exploring where system logs are stored. Logs are essential for troubleshooting problems.

### CLI METHOD

1. **Navigate to /var/log**
   - Type: `cd /var/log`
   - Press Enter
   - *You're now in the log directory*

2. **List log files**
   - Type: `ls`
   - Press Enter
   - *You will see many log files*

**What you should see:**
```
$ cd /var/log
$ ls
alternatives.log    auth.log        boot.log
apport.log          auth.log.1      boot.log.1
apt                 cloud-init.log  cups
...
$
```

3. **View system log**
   - Type: `sudo tail -20 /var/log/syslog`
   - Press Enter
   - *Enter your password when prompted*
   - *You will see the last 20 lines of the system log*

**What you should see:**
```
$ sudo tail -20 /var/log/syslog
Nov 23 15:30:01 ubuntu24 systemd[1]: Started Daily apt upgrade and clean activities.
Nov 23 15:30:05 ubuntu24 systemd[1]: Reloading.
...
$
```

4. **View authentication log**
   - Type: `sudo tail -10 /var/log/auth.log`
   - Press Enter
   - *You will see recent login attempts and authentication events*

**Common log files:**
- `/var/log/syslog` = General system messages
- `/var/log/auth.log` = Authentication and login attempts
- `/var/log/dpkg.log` = Package installation log
- `/var/log/kern.log` = Kernel messages

---

## TASK 4: EXPLORE /usr DIRECTORY (APPLICATIONS)

**What this does:** We're exploring where installed applications and programs are stored.

### CLI METHOD

1. **Navigate to /usr/bin**
   - Type: `cd /usr/bin`
   - Press Enter
   - *You're now in the user applications directory*

2. **List some applications**
   - Type: `ls | head -20`
   - Press Enter
   - *You will see many executable programs*

**What you should see:**
```
$ cd /usr/bin
$ ls | head -20
[
aa-enabled
aa-exec
apt
apt-cache
apt-get
...
$
```

3. **Count total applications**
   - Type: `ls /usr/bin | wc -l`
   - Press Enter
   - *You will see the total number of applications*

**What you should see:**
```
$ ls /usr/bin | wc -l
2847
$
```

4. **Explore /usr structure**
   - Type: `ls -d /usr/*/`
   - Press Enter
   - *You will see subdirectories*

**Common /usr subdirectories:**
- `/usr/bin/` = User applications
- `/usr/lib/` = Libraries (shared code)
- `/usr/share/` = Application data and documentation
- `/usr/local/` = Locally installed software

---

## TASK 5: EXPLORE /home DIRECTORY (USER FILES)

**What this does:** We're exploring where user home directories are stored.

### CLI METHOD

1. **Navigate to /home**
   - Type: `cd /home`
   - Press Enter
   - *You're now in the home directory*

2. **List user directories**
   - Type: `ls -l`
   - Press Enter
   - *You will see user home directories*

**What you should see:**
```
$ cd /home
$ ls -l
total 8
drwxr-xr-x 20 student student 4096 Nov 23 15:00 student
$
```

3. **Navigate to your home directory**
   - Type: `cd ~`
   - Press Enter
   - *OR type: `cd /home/student` (replace with your username)*
   - *You're now in your home directory*

4. **Verify location**
   - Type: `pwd`
   - Press Enter
   - *You will see: `/home/yourusername`*

---

## ACTIVITY 2: MANAGING PROCESSES AND SYSTEM SERVICES

**Do on:** Ubuntu Linux system

**What we're doing:** We're learning to view and manage running processes and system services. This is essential for troubleshooting and system administration.

**Goal:** Manage processes and system services using ps, top, systemctl, and kill.

**Time:** 25 minutes

---

## TASK 6: VIEW RUNNING PROCESSES

**What this does:** We're learning to see what programs are currently running on the system.

### CLI METHOD

1. **View your processes**
   - Type: `ps`
   - Press Enter
   - *You will see processes for your current user*

**What you should see:**
```
$ ps
  PID TTY          TIME CMD
 1234 pts/0    00:00:00 bash
 5678 pts/0    00:00:00 ps
$
```

**What this means:**
- `PID` = Process ID (unique number for each process)
- `TTY` = Terminal where process is running
- `TIME` = CPU time used
- `CMD` = Command/program name

2. **View all processes**
   - Type: `ps aux`
   - Press Enter
   - *You will see ALL processes on the system*

**What you should see:**
```
$ ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.1 169832 12000 ?        Ss   Nov23   0:05 /sbin/init
root         2  0.0  0.0      0     0 ?        S    Nov23   0:00 [kthreadd]
student   1234  0.0  0.2 123456 23456 pts/0    Ss   Nov23   0:00 bash
...
$
```

**What the columns mean:**
- `USER` = Who owns the process
- `PID` = Process ID
- `%CPU` = CPU usage percentage
- `%MEM` = Memory usage percentage
- `STAT` = Process status (S=sleeping, R=running, etc.)
- `COMMAND` = Program name

3. **View processes in real-time**
   - Type: `top`
   - Press Enter
   - *You will see a live view of processes*
   - *Press **q** to quit*

**What you should see:**
- A continuously updating display
- Processes sorted by CPU usage
- System information at the top (uptime, load average, etc.)

---

## TASK 7: MANAGE SYSTEM SERVICES

**What this does:** We're learning to control system services (background programs that run automatically).

### CLI METHOD (systemctl)

1. **View all services**
   - Type: `systemctl list-units --type=service | head -20`
   - Press Enter
   - *You will see a list of system services*

**What you should see:**
```
$ systemctl list-units --type=service | head -20
UNIT                      LOAD   ACTIVE SUB     DESCRIPTION
accounts-daemon.service   loaded active running Accounts Service
apparmor.service          loaded active exited  AppArmor initialization
...
$
```

2. **Check service status**
   - Type: `systemctl status ssh`
   - Press Enter
   - *You will see detailed information about the SSH service*

**What you should see:**
```
$ systemctl status ssh
● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/lib/systemd/system/ssh.service; enabled; vendor preset: enabled)
     Active: active (running) since Nov 23 10:00:00 UTC; 5h 30min ago
...
$
```

**What this shows:**
- `Loaded` = Service configuration loaded
- `Active` = Current state (active/inactive)
- `running` = Service is currently running

3. **Start a service**
   - Type: `sudo systemctl start ssh`
   - Press Enter
   - *Enter your password*
   - *Service starts (if it was stopped)*

4. **Stop a service**
   - Type: `sudo systemctl stop ssh`
   - Press Enter
   - *Service stops*

5. **Restart a service**
   - Type: `sudo systemctl restart ssh`
   - Press Enter
   - *Service restarts*

6. **Enable service to start on boot**
   - Type: `sudo systemctl enable ssh`
   - Press Enter
   - *Service will now start automatically when system boots*

7. **Disable service from starting on boot**
   - Type: `sudo systemctl disable ssh`
   - Press Enter
   - *Service will NOT start automatically on boot*

---

## TASK 8: KILL PROCESSES

**What this does:** We're learning to terminate (kill) processes that are not responding or need to be stopped.

**Warning:** Be careful with kill commands - only kill processes you understand!

### CLI METHOD

1. **Find a process to kill (example)**
   - Type: `ps aux | grep firefox`
   - Press Enter
   - *Shows Firefox processes (if running)*
   - *Note the PID (Process ID) number*

2. **Kill a process gracefully**
   - Type: `kill PID`
   - Press Enter
   - *Replace PID with the actual process ID*
   - *This sends a "terminate" signal (allows process to clean up)*

**Example:**
```
$ ps aux | grep firefox
student  5678  2.5  5.2 1234567 89012 ?  Sl  Nov23  5:30 firefox
$ kill 5678
$
```

3. **Kill a process forcefully**
   - Type: `kill -9 PID`
   - Press Enter
   - *This forces immediate termination (use only if kill doesn't work)*

**What `-9` means:**
- `-9` = SIGKILL signal (cannot be ignored)
- Forces immediate termination
- Use only when necessary (process may lose data)

4. **Kill all processes with a name**
   - Type: `killall firefox`
   - Press Enter
   - *Kills all processes named "firefox"*

**Important:** Always try `kill` first, then `kill -9` only if needed!

---

## ACTIVITY 3: USING LOG FILES FOR TROUBLESHOOTING

**Do on:** Ubuntu Linux system

**What we're doing:** We're learning to read and analyze log files to troubleshoot problems. Logs tell you what happened on the system.

**Goal:** Use log files and system journal for troubleshooting.

**Time:** 20 minutes

---

## TASK 9: VIEW SYSTEM LOGS

**What this does:** We're viewing system logs to see what's happening on the system.

### CLI METHOD

1. **View system log (syslog)**
   - Type: `sudo tail -50 /var/log/syslog`
   - Press Enter
   - *Enter your password*
   - *You will see the last 50 lines of system log*

**What you should see:**
```
$ sudo tail -50 /var/log/syslog
Nov 23 15:30:01 ubuntu24 systemd[1]: Started Daily apt upgrade and clean activities.
Nov 23 15:30:05 ubuntu24 systemd[1]: Reloading.
Nov 23 15:31:00 ubuntu24 CRON[1234]: (root) CMD (command)
...
$
```

2. **View authentication log**
   - Type: `sudo tail -20 /var/log/auth.log`
   - Press Enter
   - *You will see recent login attempts and authentication events*

**What you should see:**
```
$ sudo tail -20 /var/log/auth.log
Nov 23 15:00:01 ubuntu24 sudo: student : TTY=pts/0 ; PWD=/home/student ; USER=root ; COMMAND=/usr/bin/tail
Nov 23 15:00:05 ubuntu24 sshd[5678]: Accepted publickey for student from 192.168.1.10
...
$
```

3. **Search log for specific text**
   - Type: `sudo grep -i "error" /var/log/syslog | tail -10`
   - Press Enter
   - *You will see recent error messages*

**What this command does:**
- `grep -i "error"` = Search for "error" (case-insensitive)
- `tail -10` = Show last 10 matches

---

## TASK 10: USE SYSTEM JOURNAL (journalctl)

**What this does:** We're using journalctl, a modern tool to view system logs. It's part of systemd and provides better log viewing.

### CLI METHOD

1. **View recent journal entries**
   - Type: `sudo journalctl -n 50`
   - Press Enter
   - *You will see the last 50 journal entries*

**What you should see:**
```
$ sudo journalctl -n 50
Nov 23 15:30:01 ubuntu24 systemd[1]: Started Daily apt upgrade and clean activities.
Nov 23 15:30:05 ubuntu24 systemd[1]: Reloading.
...
$
```

2. **View journal in real-time**
   - Type: `sudo journalctl -f`
   - Press Enter
   - *You will see new log entries as they appear*
   - *Press **Ctrl + C** to stop*

3. **View logs for a specific service**
   - Type: `sudo journalctl -u ssh`
   - Press Enter
   - *You will see all logs related to SSH service*

4. **View logs since today**
   - Type: `sudo journalctl --since today`
   - Press Enter
   - *You will see all logs from today*

5. **View logs for a specific time range**
   - Type: `sudo journalctl --since "2024-11-23 10:00:00" --until "2024-11-23 12:00:00"`
   - Press Enter
   - *You will see logs from that time period*

---

## ACTIVITY 4: INSTALLING AND MANAGING SOFTWARE PACKAGES

**Do on:** Ubuntu Linux system

**What we're doing:** We're learning to install, update, and remove software using the apt package manager. This is essential for managing software on Ubuntu.

**Goal:** Install, update and remove software packages using apt.

**Time:** 25 minutes

---

## TASK 11: UPDATE PACKAGE LIST

**What this does:** We're updating the list of available software packages. This should be done before installing software.

### CLI METHOD

1. **Update package list**
   - Type: `sudo apt update`
   - Press Enter
   - *Enter your password*
   - *The system downloads the latest package information*

**What you should see:**
```
$ sudo apt update
Hit:1 http://archive.ubuntu.com/ubuntu jammy InRelease
Get:2 http://archive.ubuntu.com/ubuntu jammy-updates InRelease [119 kB]
...
Fetched 1,234 kB in 5s (246 kB/s)
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
5 packages can be upgraded. Run 'apt list --upgradable' to see them.
$
```

**What this does:**
- Downloads latest package information from repositories
- Checks for available updates
- Does NOT install anything yet

2. **View upgradable packages**
   - Type: `apt list --upgradable`
   - Press Enter
   - *You will see packages that can be updated*

---

## TASK 12: UPGRADE PACKAGES

**What this does:** We're updating installed packages to their latest versions.

### CLI METHOD

1. **Upgrade all packages**
   - Type: `sudo apt upgrade`
   - Press Enter
   - *Enter your password*
   - *You will see a list of packages to upgrade*
   - *Type **y** and press Enter to confirm*

**What you should see:**
```
$ sudo apt upgrade
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following packages will be upgraded:
  package1 package2 package3
5 upgraded, 0 newly installed, 0 to remove, 0 not upgraded.
Need to get 1.2 MB of archives.
Do you want to continue? [Y/n] y
...
$
```

**What this does:**
- Updates all installed packages to latest versions
- Keeps existing packages, just updates them

2. **Full system upgrade (recommended)**
   - Type: `sudo apt update && sudo apt upgrade -y`
   - Press Enter
   - *This updates package list AND upgrades packages*
   - *The `-y` flag automatically answers "yes"*

---

## TASK 13: INSTALL SOFTWARE PACKAGES

**What this does:** We're installing new software packages from the Ubuntu repositories.

### CLI METHOD

1. **Search for a package**
   - Type: `apt search htop`
   - Press Enter
   - *You will see packages matching "htop"*

**What you should see:**
```
$ apt search htop
Sorting... Done
Full Text Search... Done
htop/jammy 3.2.0-1build1 amd64
  interactive process viewer
$
```

2. **Install a package**
   - Type: `sudo apt install htop`
   - Press Enter
   - *Enter your password*
   - *You will see installation progress*
   - *Type **y** and press Enter to confirm*

**What you should see:**
```
$ sudo apt install htop
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following NEW packages will be installed:
  htop
0 upgraded, 1 newly installed, 0 to remove, 0 not upgraded.
Need to get 150 kB of archives.
Do you want to continue? [Y/n] y
Get:1 http://archive.ubuntu.com/ubuntu jammy/universe amd64 htop amd64 3.2.0-1build1 [150 kB]
Fetched 150 kB in 2s (75 kB/s)
Selecting previously unselected package htop.
...
Setting up htop (3.2.0-1build1) ...
$
```

3. **Verify installation**
   - Type: `which htop`
   - Press Enter
   - *You will see: `/usr/bin/htop`*

4. **Run the installed program**
   - Type: `htop`
   - Press Enter
   - *Htop opens (an improved process viewer)*
   - *Press **q** to quit*

---

## TASK 14: REMOVE SOFTWARE PACKAGES

**What this does:** We're removing software packages we no longer need.

### CLI METHOD

1. **Remove a package**
   - Type: `sudo apt remove htop`
   - Press Enter
   - *Enter your password*
   - *You will see confirmation prompt*
   - *Type **y** and press Enter*

**What you should see:**
```
$ sudo apt remove htop
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following packages will be REMOVED:
  htop
0 upgraded, 0 newly installed, 1 to remove, 0 not upgraded.
After this operation, 150 kB disk space will be freed.
Do you want to continue? [Y/n] y
...
Removing htop (3.2.0-1build1) ...
$
```

**What this does:**
- Removes the package
- Keeps configuration files (in case you reinstall)

2. **Remove package and configuration files**
   - Type: `sudo apt purge htop`
   - Press Enter
   - *Removes package AND configuration files*

3. **Autoclean and autoremove**
   - Type: `sudo apt autoclean`
   - Press Enter
   - *Removes old package files from cache*

   - Type: `sudo apt autoremove`
   - Press Enter
   - *Removes packages that are no longer needed*

---

## ACTIVITY 5: UNDERSTANDING NETWORK SETTINGS

**Do on:** Ubuntu Linux system

**What we're doing:** We're learning to view and understand network configuration. This is essential for troubleshooting network issues.

**Goal:** Understand basic IP concepts and view network settings.

**Time:** 20 minutes

---

## TASK 15: VIEW NETWORK INTERFACES

**What this does:** We're viewing network interface information to see IP addresses and network configuration.

### CLI METHOD

1. **View network interfaces**
   - Type: `ip a`
   - Press Enter
   - *You will see detailed network interface information*

**What you should see:**
```
$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:0c:29:12:34:56 brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.100/24 brd 192.168.1.255 scope global dynamic noprefixroute eth0
       valid_lft 86399sec preferred_lft 86399sec
    inet6 fe80::20c:29ff:fe12:3456/64 scope link
       valid_lft forever preferred_lft forever
$
```

**What this shows:**
- `lo` = Loopback interface (127.0.0.1 - localhost)
- `eth0` = Ethernet interface (your network connection)
- `inet 192.168.1.100/24` = IPv4 address and subnet mask
- `/24` means subnet mask 255.255.255.0

2. **View only IP addresses**
   - Type: `hostname -I`
   - Press Enter
   - *You will see just the IP addresses*

**What you should see:**
```
$ hostname -I
192.168.1.100
$
```

---

## TASK 16: VIEW ROUTING TABLE

**What this does:** We're viewing the routing table to see how network traffic is directed.

### CLI METHOD

1. **View routing table**
   - Type: `ip r`
   - Press Enter
   - *You will see the routing table*

**What you should see:**
```
$ ip r
default via 192.168.1.1 dev eth0 proto dhcp metric 100
192.168.1.0/24 dev eth0 proto kernel scope link src 192.168.1.100 metric 100
$
```

**What this means:**
- `default via 192.168.1.1` = Default gateway (router IP)
- `192.168.1.0/24` = Local network
- `dev eth0` = Uses eth0 interface

**Understanding the routing table:**
- **Default gateway:** Where traffic goes when destination is not on local network
- **Local network:** Traffic for 192.168.1.0/24 stays on local network
- **Interface:** Which network card to use

---

## TASK 17: TEST NETWORK CONNECTIVITY

**What this does:** We're testing if we can reach other devices on the network and the internet.

### CLI METHOD

1. **Ping localhost**
   - Type: `ping -c 4 127.0.0.1`
   - Press Enter
   - *Tests if network stack is working*
   - *The `-c 4` means send 4 packets then stop*

**What you should see:**
```
$ ping -c 4 127.0.0.1
PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.020 ms
64 bytes from 127.0.0.1: icmp_seq=2 ttl=64 time=0.015 ms
64 bytes from 127.0.0.1: icmp_seq=3 ttl=64 time=0.016 ms
64 bytes from 127.0.0.1: icmp_seq=4 ttl=64 time=0.017 ms

--- 127.0.0.1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3062ms
rtt min/avg/max/mdev = 0.015/0.017/0.020/0.002 ms
$
```

2. **Ping default gateway**
   - Type: `ping -c 4 192.168.1.1`
   - Press Enter
   - *Replace with your actual gateway IP (from `ip r`)*
   - *Tests if you can reach your router*

3. **Ping internet**
   - Type: `ping -c 4 8.8.8.8`
   - Press Enter
   - *Tests if you can reach the internet (8.8.8.8 is Google's DNS)*

4. **Ping by hostname**
   - Type: `ping -c 4 google.com`
   - Press Enter
   - *Tests DNS resolution and internet connectivity*

**What you should see:**
```
$ ping -c 4 google.com
PING google.com (142.250.191.14) 56(84) bytes of data.
64 bytes from 142.250.191.14: icmp_seq=1 ttl=118 time=12.345 ms
...
$
```

**What this shows:**
- DNS resolved "google.com" to IP address 142.250.191.14
- Packets are reaching the internet
- Response times are shown

---

## TASK 18: VIEW NETWORK SETTINGS (GUI METHOD)

**What this does:** We're viewing network settings using the graphical interface, which is easier for beginners.

### GUI METHOD

1. **Open Settings**
   - Click the **Settings** icon (gear icon) or press **Super key + I**
   - *Settings window opens*

2. **Navigate to Network**
   - *In Settings, you will see various categories*
   - *Click on **Network** (or **Wi-Fi** / **Wired**)*
   - *Network settings window opens*

3. **View connection details**
   - *Click on your active connection (Wi-Fi or Wired)*
   - *You will see connection details*
   - *Click the gear icon or **Details** button*

4. **View network information**
   - *In the details window, you will see:*
     - **IPv4 Address:** Your IP address (e.g., 192.168.1.100)
     - **Subnet Mask:** Network mask (e.g., 255.255.255.0)
     - **Default Route:** Gateway IP (e.g., 192.168.1.1)
     - **DNS:** DNS server addresses

**What you should see:**
- A window showing your network configuration
- IP address, subnet mask, gateway, and DNS settings
- Similar information to what `ip a` and `ip r` show

---

## Screenshots to Capture for PLR

Take these screenshots for your Personal Learning Record:

**Activity 1:**
1. Terminal showing `ls /` output showing root directory structure
2. Terminal showing `ls -l /etc` showing configuration files
3. Terminal showing `cat /etc/passwd | head -5` showing user information
4. Terminal showing `ls /var/log` showing log files
5. Terminal showing `sudo tail -20 /var/log/syslog` showing system log
6. Terminal showing `ls /usr/bin | wc -l` showing number of applications

**Activity 2:**
7. Terminal showing `ps aux` output showing running processes
8. Terminal showing `top` command output
9. Terminal showing `systemctl status ssh` showing service status
10. Terminal showing `systemctl list-units --type=service` showing services

**Activity 3:**
11. Terminal showing `sudo journalctl -n 50` showing journal entries
12. Terminal showing `sudo journalctl -u ssh` showing service-specific logs
13. Terminal showing `sudo grep -i "error" /var/log/syslog` showing error messages

**Activity 4:**
14. Terminal showing `sudo apt update` output
15. Terminal showing `sudo apt upgrade` output
16. Terminal showing `sudo apt install htop` showing package installation
17. Terminal showing `which htop` verifying installation

**Activity 5:**
18. Terminal showing `ip a` output showing network interfaces
19. Terminal showing `ip r` output showing routing table
20. Terminal showing `hostname -I` showing IP address
21. Terminal showing `ping -c 4 google.com` showing network connectivity test
22. GUI Settings window showing network configuration

---

## Assessment Notes

### For Your PLR, Document:

**Introduction:**
- What the Linux filesystem structure is and why it's organized this way
- Why understanding system operations is important
- Brief overview of tasks performed (filesystem exploration, process management, log viewing, package management, network configuration)

**Discussion:**
- Step-by-step explanation of filesystem navigation
- How processes and services work
- How to use logs for troubleshooting
- How package management works (apt)
- How to view and understand network settings
- Commands used and what they do
- When to use each tool

**Challenges:**
- Any errors encountered
- How you troubleshooted using logs
- What you learned about system administration
- Reflection on GUI vs CLI for network settings

**Summary:**
- What you achieved
- How Linux system administration differs from Windows
- Why understanding these concepts is critical
- Real-world applications of these skills

---

## Tips for Success

**Before Starting:**
- Make sure Ubuntu is running
- Verify you have sudo access
- Take notes as you explore

**During Lab:**
- Take screenshots as you go
- Note any interesting findings
- Try to understand what each command shows
- Don't modify system files unless instructed

**After Lab:**
- Document everything in PLR immediately
- Review the filesystem structure
- Practice the commands regularly
- Understand the troubleshooting process

---

## Common Issues and Solutions

**Issue:** Permission denied when viewing logs  
**Solution:** Use `sudo` before commands that need root access

**Issue:** Cannot install packages  
**Solution:** Run `sudo apt update` first, check internet connection

**Issue:** Service won't start  
**Solution:** Check service status with `systemctl status servicename`, check logs with `journalctl -u servicename`

**Issue:** Cannot ping internet  
**Solution:** Check network configuration with `ip a`, verify gateway with `ip r`, check DNS settings

**Issue:** Process won't die  
**Solution:** Try `kill PID` first, then `kill -9 PID` if needed (be careful!)

---

**Practice these commands regularly to become proficient in Linux system administration!**

