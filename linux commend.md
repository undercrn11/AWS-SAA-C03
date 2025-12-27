# linux commend



### System Management

#### Viewing System Information

- **Check System Uptime**: `uptime`
- **Display System Information**: `uname -a`
- **Show Hardware Information**: `lshw`
- **Check CPU Information**: `lscpu`
- **Memory Usage**: `free -h`
- **Disk Usage**: `df -h`
- **Detailed Disk Usage**: `du -sh /path/to/directory`

#### Managing Users and Groups

- **Add a New User**: `sudo adduser username`
- **Delete a User**: `sudo deluser username`
- **Add a User to a Group**: `sudo usermod -aG groupname username`
- **List Users**: `cut -d: -f1 /etc/passwd`
- **List Groups**: `cut -d: -f1 /etc/group`

### File Operations

#### File and Directory Management

- **List Files in a Directory**: `ls -la`
- **Change Directory**: `cd /path/to/directory`
- **Create a Directory**: `mkdir directoryname`
- **Remove a Directory**: `rmdir directoryname`
- **Copy Files**: `cp sourcefile destinationfile`
- **Move Files**: `mv sourcefile destinationfile`
- **Delete Files**: `rm filename`
- **Search for Files**: `find /path/to/search -name filename`
- **View File Content**: `cat filename`
- **Edit Files with Nano**: `nano filename`

### Network Configuration

#### Network Information and Configuration

- **Display IP Address**: `ip addr show`
- **Check Network Connections**: `netstat -tuln`
- **Ping a Host**: `ping hostname_or_ip`
- **Traceroute to a Host**: `traceroute hostname_or_ip`
- **Check Open Ports**: `sudo lsof -i -P -n | grep LISTEN`

### Package Management

#### Using APT Package Manager

- **Update Package List**: `sudo apt update`
- **Upgrade Installed Packages**: `sudo apt upgrade`
- **Install a Package**: `sudo apt install packagename`
- **Remove a Package**: `sudo apt remove packagename`
- **Search for a Package**: `apt search packagename`
- **Show Package Information**: `apt show packagename`

### Process Management

#### Managing Processes

- **View Running Processes**: `ps aux`
- **Kill a Process by PID**: `kill PID`
- **Force Kill a Process**: `kill -9 PID`
- **View Processes in Real Time**: `top`

### Permissions and Ownership

#### Changing Permissions and Ownership

- **Change File Permissions**: `chmod 755 filename`
- **Change File Owner**: `sudo chown username:groupname filename`
- **Change Group Ownership**: `sudo chgrp groupname filename`

### Disk Management

#### Managing Disk Partitions

- **List Disk Partitions**: `sudo fdisk -l`
- **Mount a Filesystem**: `sudo mount /dev/sdX1 /mnt`
- **Unmount a Filesystem**: `sudo umount /mnt`
- **Check Disk Usage by Directory**: `du -h /path/to/directory`