# Linux CLI Complete Cheat Sheet

## Table of Contents

- [Introduction & Basics](#introduction--basics)
- [File System Navigation](#file-system-navigation)
- [File Operations](#file-operations)
- [File Permissions](#file-permissions)
- [Text Processing](#text-processing)
- [Process Management](#process-management)
- [System Information](#system-information)
- [Networking](#networking)
- [Package Management](#package-management)
- [User & Group Management](#user--group-management)
- [Disk & Storage](#disk--storage)
- [Shell & Environment](#shell--environment)
- [Shortcuts & Productivity](#shortcuts--productivity)
- [Shell Scripting Basics](#shell-scripting-basics)
- [Troubleshooting & Debugging](#troubleshooting--debugging)

## Introduction & Basics

### What is Linux CLI?

- **Command Line Interface**: Text-based interface to interact with the operating system
- **Shell**: Program that interprets commands (bash, zsh, fish)
- **Terminal**: Application that runs the shell
- **Distribution**: Different Linux versions (Ubuntu, CentOS, Debian, etc.)

### Basic Command Structure

```bash
command [options] [arguments]
# Example:
ls -l /home
# ls = command, -l = option, /home = argument
```

### Getting Help

```bash
# Manual pages
man ls
man man  # Manual about manual

# Help for built-in commands
help cd
help exit

# Brief description
whatis ls
apropos "copy files"  # Search manual pages

# Command info
info ls
ls --help  # Built-in help
```

## File System Navigation

### Directory Structure

```
/
├── bin   -> Essential binaries
├── etc   -> Configuration files
├── home  -> User directories
├── tmp   -> Temporary files
├── usr   -> User programs
├── var   -> Variable data (logs)
└── root  -> Root user home
```

### Navigation Commands

```bash
# Print working directory
pwd

# Change directory
cd /path/to/directory
cd ~        # Home directory
cd          # Same as cd ~
cd ..       # Parent directory
cd -        # Previous directory
cd ../..    # Two levels up

# List directory contents
ls
ls -l       # Long listing
ls -a       # Show hidden files
ls -la      # Long listing with hidden files
ls -lh      # Human readable sizes
ls -lt      # Sort by time
ls -ltr     # Reverse time sort
ls -R       # Recursive listing
ls *.txt    # List only txt files

# Create directories
mkdir directory_name
mkdir -p parent/child/grandchild  # Create parent directories
mkdir dir1 dir2 dir3              # Multiple directories

# Remove directories
rmdir empty_dir                   # Remove empty directory
rm -r directory_name              # Remove recursively
rm -rf directory_name             # Force remove (dangerous!)
```

## File Operations

### File Management

```bash
# Create files
touch filename.txt                # Create empty file
touch file1.txt file2.txt         # Multiple files
> filename.txt                    # Create/truncate file
cat > file.txt                    # Create with content (Ctrl+D to save)

# Copy files
cp source.txt destination.txt
cp file1.txt /path/to/destination/
cp -r dir1 dir2                   # Copy directory recursively
cp -v file1 file2                 # Verbose copy
cp -i file1 file2                 # Interactive (prompt before overwrite)

# Move/rename files
mv oldname.txt newname.txt
mv file.txt /path/to/destination/
mv -i file1 file2                 # Interactive move
mv -v file1 file2                 # Verbose move

# Remove files
rm filename.txt
rm -i file.txt                    # Interactive removal
rm -f file.txt                    # Force removal
rm *.tmp                          # Remove all tmp files
```

### File Viewing & Editing

```bash
# View file contents
cat file.txt                      # Display entire file
less file.txt                     # Page through file (space=next, q=quit)
more file.txt                     # Similar to less
head file.txt                     # First 10 lines
head -n 20 file.txt               # First 20 lines
tail file.txt                     # Last 10 lines
tail -n 20 file.txt               # Last 20 lines
tail -f logfile.log               # Follow (watch) file changes

# File editors
nano file.txt                     # Simple editor
vim file.txt                      # Advanced editor
vi file.txt                       # Classic editor

# Compare files
diff file1.txt file2.txt
diff -u file1.txt file2.txt       # Unified diff
cmp file1.txt file2.txt           # Binary comparison
```

## File Permissions

### Understanding Permissions

```
-rwxr-xr--  1 user group  size date filename
│└─┴─┴─└─┴─┴─┘
│ │  │  │   │
│ │  │  │   └─ Others: r--
│ │  │  └───── Group: r-x
│ │  └──────── Owner: rwx
│ └─────────── File type: - = regular file, d = directory
└───────────── Multiple files hardlink count
```

### Permission Commands

```bash
# Change permissions
chmod 755 filename                # Owner: rwx, Group: r-x, Others: r-x
chmod u+x script.sh               # Add execute for user
chmod g-w file.txt                # Remove write for group
chmod o=r file.txt                # Set others to read only
chmod -R 755 directory/           # Recursive

# Change ownership
chown user:group filename
chown username filename           # Change user only
chown :groupname filename         # Change group only
chown -R user:group directory/    # Recursive

# Change group
chgrp groupname filename
chgrp -R groupname directory/     # Recursive

# Special permissions
chmod +t directory/               # Sticky bit
chmod u+s executable              # Setuid
chmod g+s executable              # Setgid
```

### Permission Numeric Values

```
4 = read (r)
2 = write (w)
1 = execute (x)

Examples:
755 = rwxr-xr-x
644 = rw-r--r--
777 = rwxrwxrwx
600 = rw-------
```

## Text Processing

### Viewing & Searching

```bash
# Search in files
grep "pattern" file.txt
grep -i "pattern" file.txt        # Case insensitive
grep -r "pattern" directory/      # Recursive search
grep -v "pattern" file.txt        # Invert match (exclude)
grep -n "pattern" file.txt        # Show line numbers
grep -c "pattern" file.txt        # Count matches
grep -E "regex" file.txt          # Extended regex

# Advanced text processing
awk '{print $1}' file.txt         # Print first column
awk -F: '{print $1}' /etc/passwd  # Use colon as delimiter
sed 's/old/new/g' file.txt        # Replace text
sed -i 's/old/new/g' file.txt     # In-place replacement
cut -d: -f1 /etc/passwd           # Cut first field

# Text statistics
wc file.txt                       # Line, word, character count
wc -l file.txt                    # Line count only
wc -w file.txt                    # Word count only
wc -c file.txt                    # Character count only
```

### Sorting & Filtering

```bash
# Sort lines
sort file.txt
sort -r file.txt                  # Reverse sort
sort -n file.txt                  # Numeric sort
sort -u file.txt                  # Unique lines
sort -k2 file.txt                 # Sort by second column

# Unique lines
uniq file.txt
uniq -c file.txt                  # Count occurrences
uniq -d file.txt                  # Only show duplicates

# Combine files
paste file1.txt file2.txt         # Merge lines
join file1.txt file2.txt          # Join on common field

# Text transformation
tr 'a-z' 'A-Z' < file.txt         # Convert to uppercase
tr -d '\r' < file.txt             # Remove carriage returns
```

## Process Management

### Process Monitoring

```bash
# View processes
ps                               # Current shell processes
ps aux                           # All processes detailed
ps -ef                           # All processes full format
ps aux | grep nginx              # Find specific process
ps -p PID -o pid,cmd             # Specific process info

# Real-time process monitoring
top
htop                             # Enhanced top (if installed)
top -u username                  # Show processes for user

# Process tree
pstree
pstree -p                        # Show PIDs
pstree username                  # User's processes
```

### Process Control

```bash
# Run processes
command &                        # Run in background
nohup command &                  # Run immune to hangups

# Process control
Ctrl + C                         # Interrupt process
Ctrl + Z                         # Suspend process
fg                               # Resume in foreground
bg                               # Resume in background

# Job control
jobs                             # List background jobs
kill %1                          # Kill job number 1
kill -9 %1                       # Force kill job

# Signal processes
kill PID                         # Terminate process (SIGTERM)
kill -9 PID                      # Force kill (SIGKILL)
kill -15 PID                     # Graceful termination
kill -1 PID                      # Hangup (reload config)
killall process_name             # Kill all processes by name
pkill pattern                    # Kill by pattern

# Process priority
nice -n 10 command               # Start with lower priority
renice 10 PID                    # Change priority of running process
```

## System Information

### System Overview

```bash
# System information
uname -a                         # All system info
uname -r                         # Kernel version
hostname                         # System hostname
whoami                           # Current user
id                               # User identity
who                              # Logged in users
w                                # Who and what they're doing

# Hardware information
lscpu                            # CPU information
free -h                          # Memory usage (human readable)
free -m                          # Memory in MB
df -h                            # Disk space (human readable)
du -sh directory/                # Directory size
du -h --max-depth=1 /path        # Size of first-level directories

# Uptime and load
uptime                           # System uptime and load
cat /proc/loadavg                # System load averages
```

### Date & Time

```bash
# Date and time
date                             # Current date and time
date "+%Y-%m-%d"                 # Format: 2024-01-15
date "+%H:%M:%S"                 # Format: 14:30:25
cal                              # Current month calendar
cal 2024                         # Whole year calendar
timedatectl                      # System time and date info

# Set date/time (requires root)
sudo date MMDDhhmmYYYY           # Set date manually
sudo timedatectl set-time "2024-01-15 14:30:00"
```

## Networking

### Network Configuration

```bash
# Network interfaces
ip addr show                     # Show IP addresses
ifconfig                         # Legacy interface config
ip link show                     # Network interfaces

# Network connectivity
ping google.com                  # Test connectivity
ping -c 4 google.com             # Ping 4 times
traceroute google.com            # Trace route path
mtr google.com                   # Continuous traceroute

# DNS resolution
nslookup google.com
dig google.com                   # DNS lookup
host google.com                  # Simple DNS lookup

# Network statistics
netstat -tuln                    # Listening ports
ss -tuln                         # Modern socket statistics
netstat -r                       # Routing table
ip route show                    # Show routing table
```

### Network Tools

```bash
# Download files
wget http://example.com/file.zip
wget -O filename.zip http://example.com/file.zip
curl -O http://example.com/file.txt
curl -L -o file.txt http://example.com/file.txt

# SSH connections
ssh username@hostname
ssh -p 2222 username@hostname    # Custom port
ssh -i key.pem username@hostname # Identity file

# SCP file transfer
scp file.txt user@remote:/path/
scp user@remote:/path/file.txt ./
scp -r directory/ user@remote:/path/

# Network services
telnet hostname port             # Test port connectivity
nc -zv hostname port             # Netcat port scan
```

## Package Management

### Debian/Ubuntu (APT)

```bash
# Update package lists
sudo apt update

# Upgrade packages
sudo apt upgrade
sudo apt full-upgrade

# Install packages
sudo apt install package_name
sudo apt install package1 package2

# Remove packages
sudo apt remove package_name
sudo apt purge package_name       # Remove with config files
sudo apt autoremove               # Remove unused packages

# Search packages
apt search "pattern"
apt show package_name             # Package information

# Clean up
sudo apt clean                   # Clean package cache
sudo apt autoclean               # Clean obsolete packages

# List packages
apt list --installed
dpkg -l                          # List installed packages
```

### Red Hat/CentOS (YUM/DNF)

```bash
# YUM (older versions)
sudo yum update
sudo yum install package_name
sudo yum remove package_name
sudo yum search "pattern"

# DNF (newer versions)
sudo dnf update
sudo dnf install package_name
sudo dnf remove package_name
sudo dnf search "pattern"

# RPM package management
rpm -qa                          # List all installed packages
rpm -qi package_name             # Package information
rpm -ql package_name             # Files in package
```

### Other Package Managers

```bash
# Snap packages
sudo snap install package_name
sudo snap remove package_name
snap list

# Flatpak
flatpak install package_name
flatpak remove package_name
flatpak list

# Pip (Python)
pip install package_name
pip uninstall package_name
pip list

# NPM (Node.js)
npm install package_name
npm uninstall package_name
npm list
```

## User & Group Management

### User Management

```bash
# User information
whoami                           # Current user
id                               # User ID and groups
finger username                  # User information

# Create/modify users
sudo useradd username            # Create user
sudo userdel username            # Delete user
sudo usermod -aG group username  # Add user to group
sudo passwd username             # Change user password

# Switch users
su username                      # Switch user
su - username                    # Switch with login shell
sudo -i                          # Switch to root
sudo -u username command         # Run command as user
```

### Group Management

```bash
# Group information
groups username                  # User's groups
getent group groupname           # Group information

# Create/modify groups
sudo groupadd groupname          # Create group
sudo groupdel groupname          # Delete group
sudo gpasswd -a user group       # Add user to group
sudo gpasswd -d user group       # Remove user from group
```

### Sudo Configuration

```bash
# Sudo commands
sudo command                     # Run as root
sudo -s                          # Root shell
sudo -u username command         # Run as specific user

# Sudoers file
sudo visudo                      # Edit sudoers safely
# Common sudoers entries:
# username ALL=(ALL) ALL
# %groupname ALL=(ALL) ALL
```

## Disk & Storage

### Disk Information

```bash
# Disk usage
df -h                            # Filesystem disk space
df -i                            # Inode usage
du -sh *                         # Directory sizes
du -h --max-depth=1              # Top-level directory sizes

# Disk information
lsblk                            # Block devices
fdisk -l                         # Partition tables
blkid                            # Block device attributes
mount                            # Mounted filesystems
```

### Disk Operations

```bash
# Mounting filesystems
mount /dev/sdb1 /mnt             # Mount device
umount /mnt                      # Unmount
mount -t nfs server:/path /mnt   # Mount NFS

# Filesystem checks
fsck /dev/sda1                   # Filesystem check
fsck -y /dev/sda1                # Auto-repair

# Disk benchmarking
dd if=/dev/zero of=/tmp/test bs=1M count=100  # Write test
hdparm -Tt /dev/sda              # Disk performance
```

### RAID & LVM

```bash
# RAID information
cat /proc/mdstat                 # Software RAID status
mdadm --detail /dev/md0          # RAID details

# LVM commands
pvdisplay                        # Physical volumes
vgdisplay                        # Volume groups
lvdisplay                        # Logical volumes
lvextend -L +10G /dev/vg0/lv0    # Extend logical volume
```

## Shell & Environment

### Environment Variables

```bash
# View environment
env                              # All environment variables
printenv                         # All environment variables
echo $PATH                       # Specific variable
set                              # Shell variables and functions

# Set variables
export VARIABLE=value            # Set environment variable
VARIABLE=value command           # Set for single command

# Common variables
echo $HOME                       # Home directory
echo $USER                       # Current user
echo $SHELL                      # Current shell
echo $PWD                        # Current directory
echo $PATH                       # Command search path
```

### Shell Configuration

```bash
# Shell configuration files
~/.bashrc                        # Bash run commands
~/.bash_profile                  # Bash login profile
~/.profile                       # User profile
/etc/profile                     # System profile

# Reload configuration
source ~/.bashrc
. ~/.bashrc                      # Same as source

# Shell history
history                          # Command history
!100                             # Execute command 100 from history
!!                               # Last command
!ssh                             # Last ssh command
Ctrl + R                         # Search history
```

### Input/Output Redirection

```bash
# Output redirection
command > file.txt               # Overwrite file
command >> file.txt              # Append to file
command 2> error.log             # Redirect stderr
command > output.log 2>&1        # Redirect both stdout and stderr
command &> file.txt              # Redirect both (bash)

# Input redirection
command < input.txt
command << EOF                   # Here document
multiple
lines
EOF

# Pipes
command1 | command2              # Pipe output
ls -la | grep ".txt"             # Filter output
ls -la | tee output.txt          # Output to screen and file
```

## Shortcuts & Productivity

### Keyboard Shortcuts

```bash
# Navigation
Ctrl + A                         # Beginning of line
Ctrl + E                         # End of line
Ctrl + ←                         # Previous word
Ctrl + →                         # Next word

# Editing
Ctrl + U                         # Cut to beginning
Ctrl + K                         # Cut to end
Ctrl + W                         # Cut previous word
Ctrl + Y                         # Paste cut text
Ctrl + _                         # Undo

# Process control
Ctrl + C                         # Interrupt/kill
Ctrl + Z                         # Suspend
Ctrl + D                         # End of file/exit
Ctrl + L                         # Clear screen

# History
Ctrl + R                         # Reverse search
↑ / ↓                            # Navigate history
Ctrl + P / Ctrl + N              # Previous/next command
```

### Aliases & Functions

```bash
# Create aliases
alias ll='ls -la'
alias rm='rm -i'                 # Interactive rm
alias ..='cd ..'
alias ...='cd ../..'

# List aliases
alias
type command                      # Show if command is alias

# Remove alias
unalias alias_name

# Persistent aliases (add to ~/.bashrc)
echo "alias ll='ls -la'" >> ~/.bashrc
```

### Command Chaining

```bash
# Multiple commands
command1 ; command2              # Run sequentially
command1 && command2             # Run if first succeeds
command1 || command2             # Run if first fails

# Background and grouping
(command1 ; command2)            # Run in subshell
{ command1 ; command2 ; }        # Run in current shell
command1 & command2 &            # Run both in background
```

## Shell Scripting Basics

### Script Structure

```bash
#!/bin/bash
# This is a comment
echo "Hello, World!"

# Variables
name="John"
echo "Hello, $name"

# Input
read -p "Enter your name: " username
echo "Hello, $username"

# Conditional execution
if [ $? -eq 0 ]; then
    echo "Success"
else
    echo "Failure"
fi
```

### Basic Scripting Elements

```bash
#!/bin/bash

# Variables
SCRIPT_NAME=$(basename "$0")
BACKUP_DIR="/backup/$(date +%Y%m%d)"

# Functions
create_backup() {
    echo "Creating backup in $BACKUP_DIR"
    mkdir -p "$BACKUP_DIR"
}

# Conditions
if [ -d "/var/log" ]; then
    echo "Directory exists"
elif [ ! -e "/var/log" ]; then
    echo "Does not exist"
else
    echo "Other"
fi

# Loops
for file in *.txt; do
    echo "Processing $file"
done

while read line; do
    echo "Line: $line"
done < file.txt
```

### Common Script Patterns

```bash
#!/bin/bash

# Check if run as root
if [ "$EUID" -ne 0 ]; then
    echo "Please run as root"
    exit 1
fi

# Check if file exists
if [ ! -f "$1" ]; then
    echo "File $1 not found"
    exit 1
fi

# Logging function
log() {
    echo "$(date '+%Y-%m-%d %H:%M:%S') - $1" >> /var/log/myscript.log
}

# Usage function
usage() {
    echo "Usage: $0 [options] filename"
    exit 1
}
```

## Troubleshooting & Debugging

### System Logs

```bash
# View system logs
sudo tail -f /var/log/syslog      # System logs (Ubuntu)
sudo tail -f /var/log/messages    # System logs (CentOS)
journalctl -f                     # Systemd journal
dmesg                             # Kernel messages
tail -f /var/log/auth.log         # Authentication logs

# Application logs
sudo tail -f /var/log/nginx/access.log
sudo tail -f /var/log/mysql/error.log
```

### Performance Monitoring

```bash
# Real-time monitoring
top
htop
iotop                            # I/O monitoring
nethogs                          # Network usage by process

# System performance
vmstat 1                         # Virtual memory statistics
mpstat 1                         # CPU statistics
iostat 1                         # I/O statistics
sar                              # System activity report

# Memory info
cat /proc/meminfo
free -m
```

### Debugging Tools

```bash
# Command debugging
set -x                           # Debug mode
set +x                           # Disable debug
bash -x script.sh                # Debug script

# Network debugging
tcpdump -i eth0
netstat -tuln
ss -tuln
lsof -i :80                      # What's using port 80

# File descriptor issues
lsof -p PID                      # Open files by process
lsof /path/to/file               # Processes using file
```

### Recovery & Repair

```bash
# File system repair
fsck /dev/sda1
fsck -y /dev/sda1                # Auto yes

# Boot repair
sudo grub-install /dev/sda
sudo update-grub

# Package repair
sudo apt --fix-broken install
sudo dpkg --configure -a

# Service recovery
sudo systemctl daemon-reload
sudo systemctl reset-failed
```

---

## Quick Reference Card

### Essential Commands

```bash
# File operations
ls -la, cd, pwd, cp, mv, rm, mkdir, rmdir

# File viewing
cat, less, head, tail, grep, find

# Process management
ps aux, top, kill, killall, bg, fg

# System info
df -h, free -h, uname -a, uptime

# Networking
ping, curl, wget, ssh, scp, netstat
```

### Common Patterns

```bash
# Find files
find /home -name "*.txt" -type f

# Search in files
grep -r "pattern" /path/

# Archive operations
tar -czf archive.tar.gz directory/
tar -xzf archive.tar.gz

# Permission examples
chmod 644 file.txt
chmod 755 script.sh
```

### Emergency Recovery

```bash
# Read-only root? Remount as read-write
mount -o remount,rw /

# Broken package system
sudo dpkg --configure -a
sudo apt --fix-broken install

# Full filesystem
du -sh /* | sort -hr
find /var/log -name "*.log" -size +100M
```

---

_Remember: With great power comes great responsibility. Always double-check destructive commands like `rm -rf` and be cautious with `sudo` privileges!_
