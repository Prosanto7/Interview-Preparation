# 🐧 Linux & Command Line Essentials

> Essential Linux knowledge for backend engineers and DevOps.

---

## 📚 Table of Contents

1. [Linux Basics](#-linux-basics)
2. [File System](#-file-system)
3. [Essential Commands](#-essential-commands)
4. [File Permissions](#-file-permissions)
5. [Process Management](#-process-management)
6. [Networking](#-networking)
7. [Package Management](#-package-management)
8. [Shell Scripting](#-shell-scripting)
9. [System Administration](#-system-administration)
10. [Resources](#-resources)

---

## 🎯 Linux Basics

### What is Linux?

**Linux** is an open-source Unix-like operating system kernel. Combined with GNU tools, it forms complete operating systems (distributions).

### Popular Distributions

| Distribution | Based On | Use Case |
|--------------|----------|----------|
| **Ubuntu** | Debian | Beginner-friendly, desktop & server |
| **Debian** | Independent | Stable, server environments |
| **CentOS/Rocky** | RHEL | Enterprise servers |
| **Fedora** | Independent | Cutting-edge features |
| **Arch Linux** | Independent | Advanced users, customization |
| **Alpine** | Independent | Lightweight, containers |

---

### Linux Directory Structure

```
/                   # Root directory
├── bin/            # Essential binaries (ls, cp, cat)
├── boot/           # Boot loader files
├── dev/            # Device files
├── etc/            # Configuration files
├── home/           # User home directories
│   └── username/   # Individual user directory
├── lib/            # System libraries
├── media/          # Removable media mount points
├── mnt/            # Temporary mount points
├── opt/            # Optional software
├── proc/           # Process information
├── root/           # Root user home
├── sbin/           # System binaries
├── srv/            # Service data
├── tmp/            # Temporary files
├── usr/            # User programs
│   ├── bin/        # User binaries
│   ├── lib/        # User libraries
│   └── local/      # Locally installed software
└── var/            # Variable data (logs, databases)
    ├── log/        # Log files
    └── www/        # Web server files
```

---

## 📁 File System

### Navigation

```bash
# Print working directory
pwd

# List files
ls                  # Basic listing
ls -l               # Long format (permissions, size, date)
ls -la              # Include hidden files
ls -lh              # Human-readable sizes
ls -lt              # Sort by modification time
ls -lS              # Sort by size

# Change directory
cd /path/to/dir
cd ~                # Home directory
cd ..               # Parent directory
cd -                # Previous directory

# Create directory
mkdir mydir
mkdir -p path/to/nested/dir

# Remove directory
rmdir emptydir      # Only empty directories
rm -r directory     # Recursive delete
rm -rf directory    # Force recursive delete (careful!)
```

---

### File Operations

```bash
# Create file
touch file.txt
echo "content" > file.txt

# Copy
cp source.txt dest.txt
cp -r dir1/ dir2/           # Recursive copy

# Move/Rename
mv old.txt new.txt
mv file.txt /path/to/

# Delete
rm file.txt
rm -f file.txt              # Force delete
rm -i file.txt              # Interactive (prompt)

# View file contents
cat file.txt                # Entire file
less file.txt               # Paginated view
head file.txt               # First 10 lines
head -n 20 file.txt         # First 20 lines
tail file.txt               # Last 10 lines
tail -n 20 file.txt         # Last 20 lines
tail -f log.txt             # Follow (real-time updates)

# Search in files
grep "pattern" file.txt
grep -r "pattern" /path/    # Recursive search
grep -i "pattern" file.txt  # Case-insensitive
grep -n "pattern" file.txt  # Show line numbers
grep -v "pattern" file.txt  # Invert match (exclude)

# Find files
find /path -name "*.txt"
find /path -type f -name "*.log"
find /path -mtime -7        # Modified in last 7 days
find /path -size +100M      # Larger than 100MB
```

---

### File Viewing & Editing

```bash
# Text editors
nano file.txt               # Beginner-friendly
vim file.txt                # Powerful, steep learning curve
vi file.txt                 # Classic

# Vim basics
i                           # Insert mode
Esc                         # Command mode
:w                          # Save
:q                          # Quit
:wq or :x                   # Save and quit
:q!                         # Quit without saving
/search                     # Search forward
```

---

## 🔑 File Permissions

### Understanding Permissions

```bash
-rwxrw-r-- 1 user group 1234 Jan 15 10:30 file.txt
│││││││││
│││││││││
│││││││└──── Others: read only (r--)
││││││└───── Group: read, write (rw-)
│││││└────── Owner: read, write, execute (rwx)
││││└─────── Number of hard links
│││└──────── Owner
││└───────── Group
│└────────── Size
└─────────── File type (- = file, d = directory, l = link)
```

### Permission Values

| Permission | Symbolic | Numeric |
|------------|----------|---------|
| Read | r | 4 |
| Write | w | 2 |
| Execute | x | 1 |
| None | - | 0 |

```bash
# Change permissions
chmod 755 file.sh           # rwxr-xr-x
chmod 644 file.txt          # rw-r--r--
chmod +x script.sh          # Add execute permission
chmod -w file.txt           # Remove write permission
chmod u+x file.sh           # Owner: add execute
chmod g-w file.txt          # Group: remove write
chmod o=r file.txt          # Others: set read only

# Change owner
chown user file.txt
chown user:group file.txt
chown -R user:group /path   # Recursive

# Change group
chgrp group file.txt
```

---

## ⚙️ Process Management

### Viewing Processes

```bash
# List processes
ps                          # Current shell processes
ps aux                      # All processes
ps aux | grep nginx         # Find specific process

# Real-time process viewer
top                         # Classic
htop                        # Better (needs installation)

# Process tree
pstree
```

---

### Managing Processes

```bash
# Run in background
command &

# Bring to foreground
fg

# Send to background
Ctrl+Z                      # Suspend
bg                          # Resume in background

# Kill process
kill PID
kill -9 PID                 # Force kill (SIGKILL)
killall process_name
pkill -f pattern

# Process priority
nice -n 10 command          # Lower priority
renice -n 5 -p PID          # Change priority
```

---

### System Information

```bash
# System uptime
uptime

# Memory usage
free -h

# Disk usage
df -h                       # Filesystem usage
du -sh /path                # Directory size
du -h --max-depth=1         # Subdirectory sizes

# CPU information
lscpu
cat /proc/cpuinfo

# System information
uname -a                    # Kernel info
hostnamectl                 # System info
```

---

## 🌐 Networking

### Network Configuration

```bash
# Show IP addresses
ip addr
ip a                        # Shorthand
ifconfig                    # Older command

# Show network interfaces
ip link

# Configure interface
sudo ip addr add 192.168.1.10/24 dev eth0
sudo ip link set eth0 up

# Show routing table
ip route
route -n                    # Older command
```

---

### Network Testing

```bash
# Test connectivity
ping google.com
ping -c 4 google.com        # Send 4 packets

# Trace route
traceroute google.com
tracepath google.com

# DNS lookup
nslookup google.com
dig google.com
host google.com

# Test port connectivity
telnet host port
nc -zv host port            # Netcat
```

---

### Network Tools

```bash
# Download files
wget https://example.com/file.zip
wget -O newname.zip https://example.com/file.zip

curl https://api.example.com
curl -o file.txt https://example.com/file.txt
curl -X POST -d "data" https://api.example.com

# Transfer files
scp file.txt user@host:/path/
scp user@host:/path/file.txt .
scp -r directory/ user@host:/path/

# Sync directories
rsync -avz source/ user@host:destination/
rsync -avz --delete source/ dest/  # Mirror

# Secure shell
ssh user@host
ssh -i key.pem user@host
ssh -p 2222 user@host       # Custom port
```

---

### Firewall (UFW)

```bash
# Enable firewall
sudo ufw enable

# Allow port
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow from 192.168.1.0/24

# Deny port
sudo ufw deny 23/tcp

# Status
sudo ufw status
sudo ufw status numbered

# Delete rule
sudo ufw delete 2           # By number
sudo ufw delete allow 80/tcp
```

---

## 📦 Package Management

### APT (Debian/Ubuntu)

```bash
# Update package list
sudo apt update

# Upgrade packages
sudo apt upgrade
sudo apt full-upgrade

# Install package
sudo apt install package-name
sudo apt install package1 package2

# Remove package
sudo apt remove package-name
sudo apt purge package-name  # Remove with config

# Search packages
apt search keyword

# Show package info
apt show package-name

# Auto-remove unused packages
sudo apt autoremove

# Clean cache
sudo apt clean
```

---

### YUM/DNF (RedHat/CentOS/Fedora)

```bash
# Install
sudo yum install package-name
sudo dnf install package-name  # Fedora

# Update
sudo yum update
sudo dnf upgrade

# Remove
sudo yum remove package-name

# Search
yum search keyword

# List installed
yum list installed
```

---

## 📝 Shell Scripting

### Bash Script Basics

```bash
#!/bin/bash
# Shebang - tells system to use bash

# Variables
NAME="John"
AGE=25

# Print
echo "Hello, $NAME"
echo "Age: $AGE"

# User input
read -p "Enter name: " USER_NAME
echo "Hello, $USER_NAME"

# Command substitution
CURRENT_DATE=$(date)
FILES=$(ls)

# Conditionals
if [ "$AGE" -gt 18 ]; then
    echo "Adult"
elif [ "$AGE" -eq 18 ]; then
    echo "Just turned adult"
else
    echo "Minor"
fi

# Loops
for i in {1..5}; do
    echo "Number: $i"
done

for file in *.txt; do
    echo "File: $file"
done

while [ $count -lt 10 ]; do
    echo $count
    ((count++))
done

# Functions
function greet() {
    echo "Hello, $1"
}
greet "John"

# Exit codes
command
if [ $? -eq 0 ]; then
    echo "Success"
else
    echo "Failed"
fi
```

---

### Common Script Patterns

```bash
# Check if file exists
if [ -f "/path/to/file" ]; then
    echo "File exists"
fi

# Check if directory exists
if [ -d "/path/to/dir" ]; then
    echo "Directory exists"
fi

# Check if command succeeded
if command; then
    echo "Command successful"
fi

# Multiple conditions
if [ "$a" -eq 1 ] && [ "$b" -eq 2 ]; then
    echo "Both true"
fi

# Case statement
case "$var" in
    start)
        echo "Starting..."
        ;;
    stop)
        echo "Stopping..."
        ;;
    *)
        echo "Unknown option"
        ;;
esac
```

---

## 🔧 System Administration

### Service Management (systemd)

```bash
# Start service
sudo systemctl start nginx

# Stop service
sudo systemctl stop nginx

# Restart service
sudo systemctl restart nginx

# Reload configuration
sudo systemctl reload nginx

# Enable on boot
sudo systemctl enable nginx

# Disable on boot
sudo systemctl disable nginx

# Check status
sudo systemctl status nginx

# View logs
sudo journalctl -u nginx
sudo journalctl -u nginx -f    # Follow
sudo journalctl -u nginx --since "1 hour ago"
```

---

### Cron Jobs

```bash
# Edit crontab
crontab -e

# List crontab
crontab -l

# Crontab syntax
# ┌───────────── minute (0 - 59)
# │ ┌───────────── hour (0 - 23)
# │ │ ┌───────────── day of month (1 - 31)
# │ │ │ ┌───────────── month (1 - 12)
# │ │ │ │ ┌───────────── day of week (0 - 6) (Sunday=0)
# │ │ │ │ │
# * * * * * command

# Examples
0 2 * * * /path/to/backup.sh       # Daily at 2 AM
*/15 * * * * /path/to/check.sh     # Every 15 minutes
0 0 * * 0 /path/to/weekly.sh       # Weekly on Sunday
@reboot /path/to/startup.sh        # On system boot
```

---

### User Management

```bash
# Add user
sudo adduser username
sudo useradd -m -s /bin/bash username

# Delete user
sudo deluser username
sudo userdel -r username    # Remove home directory too

# Change password
sudo passwd username
passwd                      # Change your own

# Add to group
sudo usermod -aG sudo username
sudo usermod -aG docker username

# Switch user
su - username
sudo -u username command
```

---

### Log Files

```bash
# Common log locations
/var/log/syslog             # System logs
/var/log/auth.log           # Authentication logs
/var/log/kern.log           # Kernel logs
/var/log/apache2/           # Apache logs
/var/log/nginx/             # Nginx logs

# View logs
tail -f /var/log/syslog
less /var/log/syslog
grep "error" /var/log/syslog

# Journal logs
sudo journalctl
sudo journalctl -f
sudo journalctl --since today
sudo journalctl -u nginx
```

---

## 🔗 Useful Commands & Tips

### Text Processing

```bash
# Sort lines
sort file.txt
sort -r file.txt            # Reverse
sort -n numbers.txt         # Numeric sort

# Remove duplicates
uniq file.txt
sort file.txt | uniq

# Count lines, words, characters
wc file.txt
wc -l file.txt              # Lines only
wc -w file.txt              # Words only

# Cut columns
cut -d',' -f1 file.csv      # First column, comma-separated
cut -c1-10 file.txt         # Characters 1-10

# Replace text
sed 's/old/new/' file.txt
sed 's/old/new/g' file.txt  # All occurrences
sed -i 's/old/new/g' file.txt  # Edit in place

# AWK
awk '{print $1}' file.txt   # Print first column
awk -F',' '{print $2}' file.csv  # CSV, second column
```

---

### Compression

```bash
# tar archive
tar -czf archive.tar.gz /path/to/dir    # Create
tar -xzf archive.tar.gz                 # Extract
tar -tzf archive.tar.gz                 # List contents

# zip
zip -r archive.zip /path/to/dir
unzip archive.zip
unzip -l archive.zip                    # List contents

# gzip
gzip file.txt                           # Compress
gunzip file.txt.gz                      # Decompress
```

---

### Aliases & Environment

```bash
# Create alias (temporary)
alias ll='ls -lah'
alias gs='git status'

# Permanent aliases (add to ~/.bashrc or ~/.zshrc)
echo "alias ll='ls -lah'" >> ~/.bashrc
source ~/.bashrc

# Environment variables
export PATH=$PATH:/new/path
export DB_PASSWORD="secret"

# View environment
env
printenv
echo $PATH
echo $HOME
```

---

## 📚 Resources

### Learning Platforms
- [Linux Journey](https://linuxjourney.com/)
- [The Linux Command Line Book](http://linuxcommand.org/tlcl.php) (Free)
- [OverTheWire: Bandit](https://overthewire.org/wargames/bandit/) (War game)

### Cheat Sheets
- [Linux Command Cheat Sheet](https://www.linuxtrainingacademy.com/linux-commands-cheat-sheet/)
- [Vim Cheat Sheet](https://vim.rtorr.com/)

### Documentation
- [Ubuntu Documentation](https://help.ubuntu.com/)
- [Arch Wiki](https://wiki.archlinux.org/) (Excellent for all distros)
- [Linux man pages](https://linux.die.net/man/)

### Books
- **"The Linux Command Line"** by William Shotts
- **"Linux Bible"** by Christopher Negus
- **"How Linux Works"** by Brian Ward

---

## ✅ Essential Commands Quick Reference

```bash
# Navigation
cd, ls, pwd, mkdir, rmdir

# Files
cat, less, head, tail, touch, cp, mv, rm

# Search
find, grep, locate

# Permissions
chmod, chown, chgrp

# Processes
ps, top, htop, kill, killall

# Networking
ping, curl, wget, ssh, scp, netstat

# System
df, du, free, uptime, uname

# Package
apt, yum, dnf

# Text
grep, sed, awk, sort, uniq, wc

# Archive
tar, zip, unzip, gzip
```

---

**Remember:** The best way to learn Linux is by using it daily. Set up a Linux VM or use WSL on Windows and practice!
