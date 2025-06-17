## 🐧 Linux Notes Cheatsheet

### 📦 Tar Archive

```bash
tar -xz -f filename            # Unzip .tar.gz file
tar -czf archive.tar.gz dir/   # Compress directory
tar -xvf file.tar              # Extract .tar file (not gzipped)
tar -tvf archive.tar.gz        # List contents of archive
```

### 🔍 File & Text Search

```bash
grep -ir "DATABASES = {" /opt/caleston-code/   # Search keyword recursively in files
grep -r "TODO" .                              # Search for TODO in current directory
find / -name "filename"                        # Find a file by name
find /etc -type f -name "*.conf"               # Find config files
```

---

## 🌐 Network

```bash
cat /etc/hosts             # List of hostnames and their IPs
cat /etc/resolv.conf       # DNS server configuration
ip route                   # Show routing table
ip route add IP via IP     # Add static route
ip addr                    # Show IP addresses and interfaces
hostname                   # Display system hostname
ping 8.8.8.8               # Check connectivity
traceroute google.com      # Trace path to remote host
netstat -tuln              # Show open ports
ss -tulnp                  # Show listening ports with process info
```

---

## ✍️ VIM Shortcuts

```text
yy       # Copy line
p        # Paste
dd       # Delete line
u        # Undo
Ctrl + r # Redo
:x       # Save and quit
:q!      # Quit without saving
```

---

## 🔒 Security

```bash
id username                         # Show UID, GID and groups
who                                 # Show who is logged in
last                                # Show login history
su - / sudo -u username             # Switch user
cat /etc/sudoers                    # View sudo privileges
```

### User Management

```bash
grep -i ^root /etc/passwd           # View root user info
grep -i ^bob /etc/passwd
# Format: USERNAME:PASSWORD:UID:GID:GECOS:HOMEDIR:SHELL

grep -i ^bob /etc/shadow
# Format: USERNAME:PASSWORD:LASTCHANGE:MINAGE:MAXAGE:WARN:INACTIVE:EXPDATE

grep -i ^bob /etc/group
# Format: NAME:PASSWORD:GID:MEMBERS

useradd bob
passwd bob
grep -i bob /etc/passwd
useradd -u 1009 -g 1009 -d /home/Robert -s /bin/bash -c "Mercury Project member" bob
userdel bob

# Group management
groupadd -g 1011 developer
groupdel developer
```

### Permissions

```bash
ls -l filename
chmod u+rwx filename
chmod ugo+r-x filename   # Add read, remove execute
chmod 0-rwx filename     # Remove all permissions
chmod 760 filename       # rwx for user, rw for group, none for others
chown owner:group filename
chown bob filename       # Change owner
chgrp android filename   # Change group
sudo chown -R mercury /opt/caleston-code/   # Recursive ownership
```

---

## 📤 File Transfer

```bash
scp path/filename hostname:/path/       # Copy file to remote
scp -pr path/filename hostname:/path/   # Recursive copy
rsync -avz file user@remote:/path/      # Efficient sync
ssh-copy-id username@target_host        # Copy SSH key
```

---

## 🔥 Iptables - Filtrage reseau de bas niveau
Utilise 'iptables' pour controler le trafic entrant/sortant

```bash
sudo apt install iptables
sudo iptables -L                        # List rules

iptables -A INPUT -p tcp -d 172.16.238.187 --dport 22 -j ACCEPT
iptables -A INPUT -p tcp --dport 22 -j DROP
iptables -A OUTPUT -p tcp --dport 443 -j DROP
iptables -A OUTPUT -p tcp -d 172.16.238.15 --dport 5432 -j ACCEPT
iptables -I OUTPUT -p tcp -d 172.16.238.100 --dport 443 -j ACCEPT
iptables -D OUTPUT 4                    # Delete rule #4

sudo netstat -natulp | grep postgres | grep LISTEN   # View open ports for postgres
```

---

## 🕒 Crontab

```bash
crontab -l          # List cron jobs
crontab -e          # Edit cron jobs
```
Example:
```cron
0 2 * * * /usr/local/bin/backup.sh    # Run script every day at 2 AM
0: minutes
2: heures
*: jour du mois(1-30)
*: Mois de l'annee(1-12) 
*: jour de la semaine(1-7) 
```

---

## 🔀 Services (.sh to Systemd)

### Create Service File

`/etc/systemd/system/name_project.service`
```ini
[Service]
ExecStart=/bin/bash /usr/bin/name_project.sh
```

```bash
systemctl start name_project.service
systemctl status name_project.service
systemctl stop name_project.service
```

### Persistent Service with Systemd
```ini
[Unit]
Description=Python Django for Name_Project
Documentation=http://wiki.caleston-dev/name_project
After=postgresql.service

[Service]
WorkingDirectory=.......
ExecStart=/usr/bin/name_project.sh
User=name_project
Restart=on-failure
RestartSec=10

[Install]
WantedBy=graphical.target
```

### Manage with systemctl

```bash
systemctl start/stop/restart/reload/enable/disable name_project.service
systemctl daemon-reload
systemctl edit name_project.service --full

systemctl get-default
systemctl set-default multi-user.target
systemctl list-units --all

journalctl
journalctl -b
journalctl -u name_project.service
```

To start service manually:
```bash
python3 manage.py migrate
python3 manage.py runserver 0.0.0.0:8000
```

---

## 💾 Disk Partition & Mounting

```bash
lsblk                        # List block devices
gdisk /dev/sdb              # Partition 
# Use :n to create, :w to write

sudo fdisk -l                # List partitions
sudo df -h                   # Show disk usage
blkid /dev/vdc               # Show file system type

# Mount permanently
sudo vim /etc/fstab
/dev/vdb /mnt/data ext4 rw 0 0

# Mount manually
sudo mount /dev/vdb /mnt/data

# Unmount
sudo umount /mnt/data

# LVM (Logical Volume Manager)
pvcreate /dev/vdc            # Create physical volume
vgcreate vgname /dev/vdc     # Create volume group
lvcreate -L 500M -n lvname vgname   # Create logical volume
mkfs.ext4 /dev/vgname/lvname       # Format volume
mount /dev/vgname/lvname /mnt/data # Mount logical volume
```

---