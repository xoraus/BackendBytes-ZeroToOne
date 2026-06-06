# Linux Fundamentals

## Overview

**Linux** powers the vast majority of servers on the internet — from cloud platforms like AWS and Google Cloud to containerized microservices running on Kubernetes. As a backend developer, you'll spend significant time working with Linux: deploying applications, debugging issues, managing servers, and writing automation scripts. This tutorial covers the essential Linux knowledge you need to be productive in any server environment.

> **Key Insight**: Linux is **case-sensitive** and **file-centric**. Everything in Linux is a file — devices, processes, network sockets, and even system information. Understanding this philosophy helps you navigate and manipulate the system effectively.

---

## The Linux File System

### File System Hierarchy

```
/
├── bin/          Essential user binaries (ls, cat, mv)
├── boot/         Boot loader files
├── dev/          Device files
├── etc/          System configuration files
├── home/         User home directories
│   ├── alice/
│   └── bob/
├── lib/          Essential shared libraries
├── media/        Mount points for removable media
├── mnt/          Temporary mount points
├── opt/          Optional software packages
├── proc/         Virtual filesystem (process info)
├── root/         Root user's home directory
├── run/          Runtime variable data
├── sbin/         Essential system binaries
├── srv/          Service data
├── sys/          Virtual filesystem (system info)
├── tmp/          Temporary files
├── usr/          User programs and data
│   ├── bin/      Non-essential binaries
│   ├── lib/      Libraries
│   └── local/    Locally installed software
└── var/          Variable data (logs, caches)
    ├── log/      System logs
    └── tmp/      Temporary files that persist reboot
```

### Important Directories for Developers

| Directory | Purpose | Examples |
|-----------|---------|----------|
| `/etc` | Configuration files | `/etc/nginx/nginx.conf`, `/etc/hosts` |
| `/var/log` | Log files | `/var/log/nginx/access.log` |
| `/home/user` | Your personal space | `~/projects`, `~/.bashrc` |
| `/tmp` | Temporary files | Session data, downloads |
| `/usr/local` | Locally compiled software | `/usr/local/bin/node` |
| `/opt` | Optional packages | `/opt/docker` |

---

## Essential Commands

### Navigation

```bash
pwd                           # Print working directory
ls                            # List files
ls -la                        # List all files (including hidden) with details
ls -lh                        # Human-readable sizes (KB, MB)
cd /path/to/dir              # Change directory
cd ~                          # Go to home directory
cd ..                         # Go up one directory
cd -                          # Go to previous directory
```

### File Operations

```bash
cat file.txt                  # Display file contents
less file.txt                 # View with pagination (q to quit)
head -n 20 file.txt           # Show first 20 lines
tail -n 20 file.txt           # Show last 20 lines
tail -f /var/log/app.log      # Follow log in real-time

mkdir mydir                   # Create directory
mkdir -p parent/child         # Create nested directories
rmdir emptydir                # Remove empty directory
rm file.txt                   # Remove file
rm -r directory/              # Remove directory recursively
rm -rf directory/             # Force remove (DANGEROUS!)

cp file.txt backup.txt        # Copy file
cp -r dir1/ dir2/            # Copy directory
mv file.txt newname.txt       # Rename/move file
mv file.txt /dest/path/       # Move to directory

touch file.txt                # Create empty file or update timestamp
```

### Viewing File Details

```bash
ls -la
# -rw-r--r--  1 alice staff  1234 Jan 15 10:30 file.txt
# │└┬┘└┬┘└┬┘   │   │     │    │      │       │
# │ │  │  │    │   │     │    │      │       └─ Filename
# │ │  │  │    │   │     │    │      └─ Modification time
# │ │  │  │    │   │     │    └─ File size (bytes)
# │ │  │  │    │   │     └─ Group
# │ │  │  │    │   └─ Owner
# │ │  │  │    └─ Hard links
# │ │  │  └─ Others permissions
# │ │  └─ Group permissions
# │ └─ Owner permissions
# └─ File type (- = file, d = directory, l = link)
```

---

## File Permissions

### Understanding Permission Bits

```
-rwxr-xr-x  (755)
│└┬┘└┬┘└┬┘
│ │  │  │
│ │  │  └── Others: r-x (read, execute)
│ │  └───── Group:  r-x (read, execute)
│ └──────── Owner:  rwx (read, write, execute)
└────────── File type
```

| Permission | File | Directory |
|-----------|------|-----------|
| **Read (r)** | View contents | List files |
| **Write (w)** | Modify contents | Create/delete files |
| **Execute (x)** | Run as program | Enter/access directory |

### Numeric Permissions

| Number | Binary | Permissions |
|--------|--------|-------------|
| 7 | 111 | rwx |
| 6 | 110 | rw- |
| 5 | 101 | r-x |
| 4 | 100 | r-- |
| 0 | 000 | --- |

```bash
chmod 755 script.sh           # rwxr-xr-x
chmod 644 file.txt            # rw-r--r--
chmod 700 private.key         # rwx------

# Symbolic mode
chmod u+x script.sh           # Add execute for owner
chmod g-w file.txt            # Remove write for group
chmod o+r file.txt            # Add read for others
chmod a+x script.sh           # Add execute for all
```

### Ownership

```bash
chown alice file.txt          # Change owner
chown alice:developers file.txt  # Change owner and group
chown -R alice:alice /var/www  # Recursive change

chgrp developers file.txt     # Change group only
```

### Special Permissions

```bash
# SetUID (4) — run as file owner
chmod 4755 program           # -rwsr-xr-x

# SetGID (2) — run as file group
chmod 2755 directory         # drwxr-sr-x

# Sticky Bit (1) — only owner can delete
chmod 1777 /tmp              # drwxrwxrwt
```

---

## Text Processing

### grep — Pattern Search

```bash
grep "error" log.txt          # Find lines containing "error"
grep -i "error" log.txt       # Case-insensitive
grep -v "error" log.txt       # Invert match (lines WITHOUT "error")
grep -n "error" log.txt       # Show line numbers
grep -r "TODO" src/           # Recursive search
grep -E "error|warning" log.txt  # Extended regex (OR)
grep -c "error" log.txt       # Count matches
```

### sed — Stream Editor

```bash
# Replace text
sed 's/old/new/' file.txt           # Replace first occurrence per line
sed 's/old/new/g' file.txt          # Replace all occurrences
sed 's/old/new/2' file.txt          # Replace 2nd occurrence only
sed -i 's/old/new/g' file.txt       # Edit in place

# Delete lines
sed '/pattern/d' file.txt           # Delete lines matching pattern
sed '5d' file.txt                   # Delete line 5
sed '1,10d' file.txt                # Delete lines 1-10

# Print specific lines
sed -n '5,10p' file.txt             # Print lines 5-10
```

### awk — Text Processing

```bash
# Print specific columns
awk '{print $1}' file.txt           # First column
awk '{print $1, $3}' file.txt       # First and third
awk -F: '{print $1}' /etc/passwd    # Use : as delimiter

# With conditions
awk '$3 > 100 {print $1}' data.txt  # Print column 1 where column 3 > 100

# Sum a column
awk '{sum += $1} END {print sum}' numbers.txt

# Format output
awk '{printf "%-10s %5d\n", $1, $2}' data.txt
```

### cut — Column Extraction

```bash
cut -d',' -f1,3 file.csv          # Extract columns 1 and 3 from CSV
cut -c1-10 file.txt               # Extract characters 1-10
cut -d':' -f1 /etc/passwd         # Extract usernames
```

### sort and uniq

```bash
sort file.txt                     # Sort alphabetically
sort -n numbers.txt               # Sort numerically
sort -r file.txt                  # Reverse sort
sort -k2 file.txt                 # Sort by 2nd column

uniq file.txt                     # Remove adjacent duplicates
uniq -c file.txt                  # Count occurrences
sort file.txt | uniq -c | sort -nr  # Frequency count (most common first)
```

### Pipes and Redirection

```bash
# Pipe: send output of one command to another
cat log.txt | grep "error" | wc -l

# Redirection
command > file.txt                # Overwrite output to file
command >> file.txt               # Append output to file
command < input.txt               # Read input from file
command 2> errors.txt             # Redirect stderr
command &> all.txt                # Redirect stdout and stderr
command > out.txt 2>&1            # Same as above

# Here document
cat << EOF > config.txt
KEY=value
DEBUG=true
EOF
```

---

## Process Management

### Viewing Processes

```bash
ps aux                        # All processes, detailed
ps aux | grep node            # Find Node.js processes
top                           # Interactive process viewer
htop                          # Better top (if installed)

# Specific process info
pidof nginx                   # Get PID of nginx
cat /proc/1234/status         # Detailed process info
```

### Managing Processes

```bash
# Foreground/Background
./long-running-script.sh      # Run in foreground
Ctrl+Z                        # Suspend (send to background)
bg                            # Resume in background
fg                            # Bring to foreground
jobs                          # List background jobs

# Run in background from start
./script.sh &

# nohup — survive logout
nohup ./server.js &

# Kill processes
kill 1234                     # Send SIGTERM (graceful)
kill -9 1234                  # Send SIGKILL (force)
killall node                  # Kill all node processes
pkill -f "node server.js"     # Kill by pattern
```

### Signals

| Signal | Number | Description |
|--------|--------|-------------|
| SIGHUP | 1 | Hang up (reload config) |
| SIGINT | 2 | Interrupt (Ctrl+C) |
| SIGQUIT | 3 | Quit (Ctrl+\) |
| SIGKILL | 9 | Kill immediately (cannot be caught) |
| SIGTERM | 15 | Terminate (default, can be caught) |
| SIGUSR1 | 10 | User-defined |

### Process Priority

```bash
nice -n 10 ./script.sh        # Start with lower priority
renice 5 -p 1234              # Change priority of running process
```

---

## Disk and System Info

```bash
# Disk usage
df -h                         # Disk space (human-readable)
du -sh /var/log               # Directory size
du -h --max-depth=1 .         # Size of each subdirectory

# Memory
free -h                       # Memory usage
vmstat 1 5                    # Virtual memory stats (5 samples, 1 sec apart)

# CPU
lscpu                         # CPU info
uptime                        # Load average
cat /proc/loadavg             # Load average details

# System info
uname -a                      # Kernel info
uname -r                      # Kernel release
cat /etc/os-release           # OS version
hostname                      # System hostname
whoami                        # Current user
id                            # User and group IDs
```

---

## Environment Variables

```bash
# View all
env
printenv

# View specific
printenv PATH
echo $PATH

# Set temporarily
export MY_VAR="hello"

# Set permanently (add to ~/.bashrc or ~/.bash_profile)
echo 'export MY_VAR="hello"' >> ~/.bashrc
source ~/.bashrc

# Path manipulation
export PATH="$PATH:/usr/local/bin"
export PATH="/usr/local/bin:$PATH"  # Prepend (higher priority)
```

### Common Environment Variables

| Variable | Purpose |
|----------|---------|
| `PATH` | Directories to search for executables |
| `HOME` | User's home directory |
| `USER` | Current username |
| `SHELL` | Default shell |
| `LANG` | System language/locale |
| `EDITOR` | Default text editor |
| `NODE_ENV` | Node.js environment (development/production) |

---

## Shell Scripting Basics

### Shebang and Basics

```bash
#!/bin/bash

# Variables
NAME="World"
echo "Hello, $NAME!"

# Command substitution
CURRENT_DATE=$(date)
FILES=$(ls)

# User input
read -p "Enter your name: " USER_NAME
echo "Hello, $USER_NAME!"
```

### Conditionals

```bash
#!/bin/bash

if [ "$1" = "start" ]; then
    echo "Starting server..."
    node server.js &
elif [ "$1" = "stop" ]; then
    echo "Stopping server..."
    pkill -f "node server.js"
else
    echo "Usage: $0 {start|stop}"
    exit 1
fi

# Numeric comparison
if [ "$COUNT" -gt 10 ]; then
    echo "Greater than 10"
fi

# File checks
if [ -f "config.json" ]; then
    echo "Config exists"
fi

if [ -d "node_modules" ]; then
    echo "Dependencies installed"
fi
```

### Loops

```bash
#!/bin/bash

# For loop
for file in *.txt; do
    echo "Processing: $file"
done

# While loop
COUNTER=0
while [ $COUNTER -lt 5 ]; do
    echo "Count: $COUNTER"
    COUNTER=$((COUNTER + 1))
done

# Loop over lines
while IFS= read -r line; do
    echo "$line"
done < file.txt
```

### Functions

```bash
#!/bin/bash

log_message() {
    local level="$1"
    local message="$2"
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] [$level] $message"
}

log_message "INFO" "Server starting"
log_message "ERROR" "Connection failed"
```

---

## Package Managers

### apt (Debian/Ubuntu)

```bash
sudo apt update                 # Update package lists
sudo apt upgrade                # Upgrade installed packages
sudo apt install nginx          # Install package
sudo apt remove nginx           # Remove package
sudo apt autoremove             # Remove unused dependencies
apt search nodejs               # Search packages
apt show nodejs                 # Package details
```

### yum/dnf (RHEL/CentOS/Fedora)

```bash
sudo yum update                 # Update packages
sudo yum install nginx          # Install
sudo yum remove nginx           # Remove
sudo yum search nodejs          # Search
```

### Homebrew (macOS/Linux)

```bash
brew update                     # Update brew
brew upgrade                    # Upgrade packages
brew install node               # Install
brew uninstall node             # Remove
brew search nginx               # Search
brew services start nginx       # Start service
```

---

## SSH and Remote Access

```bash
# Connect to remote server
ssh user@server.com
ssh -i ~/.ssh/id_rsa user@server.com  # With key

# Copy files
scp file.txt user@server:/path/
scp -r localdir/ user@server:/path/
rsync -avz localdir/ user@server:/path/  # Better for large transfers

# SSH config (~/.ssh/config)
Host myserver
    HostName server.com
    User alice
    IdentityFile ~/.ssh/id_rsa
    Port 2222

# Then just: ssh myserver
```

### SSH Key Management

```bash
# Generate key pair
ssh-keygen -t ed25519 -C "alice@example.com"

# Copy public key to server
ssh-copy-id user@server.com

# Add key to agent
ssh-add ~/.ssh/id_ed25519
```

---

## Scheduled Tasks (Cron)

```bash
# Edit crontab
crontab -e

# List crontab
crontab -l

# Format:
# ┌───────────── minute (0 - 59)
# │ ┌───────────── hour (0 - 23)
# │ │ ┌───────────── day of month (1 - 31)
# │ │ │ ┌───────────── month (1 - 12)
# │ │ │ │ ┌───────────── day of week (0 - 6)
# │ │ │ │ │
# * * * * * command

# Examples:
0 2 * * * /backup/script.sh           # Daily at 2 AM
*/5 * * * * /health-check.sh          # Every 5 minutes
0 0 * * 0 /weekly-report.sh           # Weekly on Sunday
0 0 1 * * /monthly-cleanup.sh         # Monthly on 1st
```

---

## Common Mistakes

### Mistake 1: Running Commands as Root Unnecessarily

```bash
# ❌ Always running as root
sudo npm install
sudo node server.js

# ✅ Use sudo only when needed
npm install                    # As regular user
sudo systemctl restart nginx   # Only for system services

# ✅ Create a dedicated user for apps
sudo useradd -m -s /bin/bash nodeapp
sudo chown -R nodeapp:nodeapp /var/www/app
```

### Mistake 2: `rm -rf /`

```bash
# ❌ THE MOST DANGEROUS COMMAND
rm -rf /
rm -rf /*
rm -rf / home/user/file       # Space after / = disaster!

# ✅ Be careful with rm
rm -rf ./directory            # Explicit relative path
rm -i file.txt                # Interactive (asks confirmation)
alias rm='rm -i'              # Add to .bashrc
```

### Mistake 3: Not Quoting Variables

```bash
# ❌ Unquoted variables break on spaces
rm $file
# If file="my file.txt", tries to remove "my" and "file.txt"

# ✅ Always quote variables
rm "$file"

# ❌ Word splitting issues
for f in $files; do ... done

# ✅ Quote to preserve as single items
for f in "$files"; do ... done
```

### Mistake 4: Editing Files Without Backups

```bash
# ❌ In-place edit without backup
sed -i 's/old/new/g' config.json

# ✅ Create backup first
cp config.json config.json.bak
sed -i 's/old/new/g' config.json

# ✅ Or use version control
git diff config.json            # See what changed
git checkout -- config.json     # Revert if needed
```

### Mistake 5: Not Understanding Exit Codes

```bash
# ❌ Ignoring failures
rm file.txt
mkdir /new/dir                  # Runs even if rm failed!

# ✅ Check exit codes
rm file.txt && mkdir /new/dir   # mkdir only if rm succeeded
rm file.txt || echo "Failed"    # Echo only if rm failed

# In scripts
set -e                          # Exit on any error
set -u                          # Error on undefined variables
set -o pipefail                 # Catch errors in pipes
```

---

## Practice Exercises

### Exercise 1: File System Navigation

Complete these tasks using only the command line:

```bash
# 1. Create this structure:
# ~/practice/
# ├── docs/
# │   ├── readme.txt
# │   └── guide.txt
# ├── scripts/
# │   └── backup.sh
# └── data/
#     └── users.csv

# 2. Set permissions so:
#    - Owner can do everything
#    - Group can read and execute
#    - Others can only read

# 3. Count total lines in all .txt files

# 4. Find all files modified in the last 24 hours
```

### Exercise 2: Log Analysis

Given a web server log file (`access.log`), extract:

```bash
# 1. Top 10 IP addresses by request count
awk '{print $1}' access.log | sort | uniq -c | sort -nr | head -10

# 2. Number of 404 errors
grep ' 404 ' access.log | wc -l

# 3. Requests per hour
awk '{print $4}' access.log | cut -d: -f2 | sort | uniq -c

# 4. Most requested URLs
awk '{print $7}' access.log | sort | uniq -c | sort -nr | head -10
```

### Exercise 3: Process Monitoring Script

Write a script that:

```bash
#!/bin/bash
# 1. Checks if a Node.js process is running
# 2. If not, starts it and logs to /var/log/myapp.log
# 3. Sends an alert if the process crashes 3 times in 5 minutes
# 4. Rotates logs when they exceed 100MB
```

### Exercise 4: SSH Key Setup

```bash
# 1. Generate a new SSH key pair
# 2. Copy the public key to a remote server
# 3. Configure ~/.ssh/config for easy access
# 4. Test passwordless login
# 5. Set up SSH agent forwarding
```

### Exercise 5: System Monitoring Dashboard

Create a script that displays:

```bash
#!/bin/bash
# System Monitor Dashboard
# - Current date/time
# - Uptime and load average
# - Memory usage (used/total)
# - Disk usage for / partition
# - Top 5 CPU-consuming processes
# - Network connections count

# Output should be formatted nicely
```

---

## Summary

- Linux organizes everything as **files** in a hierarchical structure starting at `/`
- **File permissions** use owner/group/others with read/write/execute bits
- **chmod** changes permissions; **chown** changes ownership
- **grep**, **sed**, **awk**, and **cut** are the text processing power tools
- **Pipes (`|`)** chain commands; **redirection (`>`, `>>`)** sends output to files
- **ps**, **top**, **htop** monitor processes; **kill** terminates them
- **Environment variables** configure your shell and applications
- **Shell scripts** automate repetitive tasks with variables, conditionals, and loops
- **Package managers** (apt, yum, brew) install and manage software
- **SSH** provides secure remote access; keys are more secure than passwords
- **Cron** schedules recurring tasks
- Always **quote variables**, **avoid `rm -rf`**, and **use version control**

---

## Next Steps

- **Backend Development with Node.js** — deploy applications on Linux servers
- **System Design** — understand how Linux powers distributed systems
- **Docker** — containerize applications (runs on Linux内核)
- Learn **systemd** for service management and **journalctl** for log management

Happy coding! 🚀
