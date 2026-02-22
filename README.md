# Linux-ADDS-Documentation
Technical documentation for deploying an ADDS Server on Linux
# SPRINT 1 - Creation of the Domain Controller

## Configure Static IP (172.30.20.0/24)

In this case we edit the network card set to bridge adapter from the installation and set the address to 172.30.20.69.

---

In the installation we also configure the machine name and the username.

Once installed, we proceed to configure the IP for the internal network card: 10.2.10.254.

---

We apply the IP configuration and confirm it has been applied with `ip a`.

To start installing Samba we first run an update and then an upgrade.

```bash
sudo apt update
sudo apt upgrade
```

---

We install Samba with the following command:

```bash
sudo apt install samba krb5-config winbind smbclient dnsutils
```

We rename the Samba configuration file:

```bash
sudo mv /etc/samba/smb.conf /etc/samba/smb.conf.orig
```

We provision the forest:

```bash
sudo samba-tool domain provision --use-rfc2307 --interactive
```

We copy the generated Kerberos file:

```bash
sudo cp /var/lib/samba/private/krb5.conf /etc/krb5.conf
```

We enable the Active Directory service:

```bash
sudo systemctl enable samba-ad-dc
sudo systemctl start samba-ad-dc
```

---

We verify the domain:

```bash
sudo samba-tool domain level show
```

# SPRINT 2 - Join Client, Users and Groups

## Step 1 — Change the Client IP

First we change the IP of the client machine by editing the netplan configuration file `/etc/netplan/01-network-manager-all.yaml`:

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      dhcp4: no
      addresses:
        - 10.2.10.253/24
      routes:
        - to: default
          via: 172.30.20.1
      nameservers:
        addresses:
          - 10.2.10.254
          - 10.239.3.7
        search: [lab10.lan]
    enp0s8:
      dhcp4: true
```

## Step 2 — Verify Connectivity from Client to Server

We verify connectivity by pinging the server from the client:

```bash
ping 10.2.10.254
```

## Step 3 — DNS Lookup

We run an nslookup against the domain:

```bash
nslookup lab10.lan
```

We set the server IP in `/etc/resolv.conf`:

```
nameserver 127.0.0.53
options edns0 trust-ad
search lab10.lan
```

## Step 4 — Ping the FQDN

We ping the server using its Fully Qualified Domain Name (FQDN):

```bash
ping ls10.lab10.lan
```

## Step 5 — Update the Machine

We update and upgrade the client machine:

```bash
sudo apt update && sudo apt upgrade
```

## Step 6 — Install Required Packages

After updating, we install the following packages:

```bash
sudo apt install realmd sssd sssd-tools libnss-sss libpam-sss adcli -y
```

## Step 7 — Discover the Domain

We discover the domain:

```bash
realm discover lab10.lan
```

## Step 8 — Join the Domain

We join the domain using the administrator account:

```bash
sudo realm join lab10.lan -U administrator --verbose
```

## Step 9 — Enable Home Directory Creation

We enable automatic home directory creation on first login:

```bash
sudo pam-auth-update --enable mkhomedir
```

## Step 10 — Create Users in Samba (from the server)

We create users on the Samba domain controller:

```bash
sudo samba-tool user create <username> <password>
```

Example:

```bash
sudo samba-tool user create Alice admin_21
sudo samba-tool user create Bob admin_21
sudo samba-tool user create Charlie admin_21
```

## Step 11 — Create Groups

We add groups to the domain:

```bash
sudo samba-tool group add <group_name>
```

Example:

```bash
sudo samba-tool group add IT_Admins
sudo samba-tool group add Students
sudo samba-tool group add HR_Staff
```

## Step 12 — Verify Groups

We verify the groups have been created:

```bash
sudo samba-tool group list | grep -E "(IT_Admins|HR_Staff|Students|Finance)"
```

## Step 13 — Add Users to Their Respective Groups

We add each user to their corresponding group:

```bash
sudo samba-tool group addmembers <group> <user>
```

Example:

```bash
sudo samba-tool group addmembers IT_Admins Alice
sudo samba-tool group addmembers Students Bob
sudo samba-tool group addmembers Students Charlie
```

## Step 14 — Verify Users from the Client

We verify the users were created successfully by logging into the client machine as one of the domain users:

```bash
# Login as alice@lab10.lan from the client
```

## Step 15 — Create Organizational Units (OUs)

We create Organizational Units on the domain controller:

```bash
sudo samba-tool ou create "OU=<name>,DC=<domain>,DC=<tld>"
```

Example:

```bash
sudo samba-tool ou create "OU=IT_Department,DC=lab10,DC=lan"
sudo samba-tool ou create "OU=HR_Department,DC=lab10,DC=lan"
sudo samba-tool ou create "OU=Students,DC=lab10,DC=lan"
```

## Step 16 — Create Users Directly Inside an OU

We can also create users and assign them to an OU at creation time:

```bash
sudo samba-tool user create <name> <password> --userou="OU=<name>"
```

Example:

```bash
sudo samba-tool user create Dave admin_21 --userou="OU=IT_Department"
```

## Step 17 — Verify the User Structure

We check the full user structure to confirm all users and OUs are correctly placed:

```bash
sudo samba-tool user list --full-dn
```

# SPRINT 3 - Disks and Shared Folders

## Step 1 — Add a New Disk

First we add a new disk to the machine. We identify it using `lsblk`. In this case it is the 2G disk (`/dev/sdb`).

```bash
lsblk
```

## Step 2 — Create the Disk Partition

We create a partition on the new disk using `fdisk`:

```bash
sudo fdisk /dev/sdb
```

Inside fdisk, we follow these steps:
- Press `n` to create a new partition
- Select `p` for primary
- Accept the default partition number (1)
- Accept the default first and last sectors (full disk)
- Press `w` to write the changes and exit

## Step 3 — Format with ext4

We format the new partition with the ext4 filesystem:

```bash
sudo mkfs.ext4 /dev/sdb1
```

## Step 4 — Create a Mount Point and Mount the Partition

We create a new mount point and mount the partition there:

```bash
sudo mkdir -p /mnt/discn
sudo mount /dev/sdb1 /mnt/discn
```

## Step 5 — Verify the Mount

We verify the partition is correctly mounted:

```bash
df -h | grep sdb
```

## Step 6 — Create the Shared Folders

We create the directory structure for the shared folders:

```bash
sudo mkdir -p /mnt/discn/compar/finance
sudo mkdir -p /mnt/discn/compar/hr
sudo mkdir -p /mnt/discn/compar/public
```

## Step 7 — Set Permissions on the Folders

We set recursive permissions on the shared folder:

```bash
sudo chmod -R 770 /mnt/discn/compar/
```

## Step 8 — Check the Permissions

We verify the permissions have been applied:

```bash
sudo ls -la /mnt/discn/compar
```

## Step 9 — Configure Shared Folders in smb.conf

We configure the shared folders by editing `/etc/samba/smb.conf`:

```ini
idmap_ldb:use rfc2307 = yes

[sysvol]
    path = /var/lib/samba/sysvol
    read only = No

[netlogon]
    path = /var/lib/samba/sysvol/lab10.lan/scripts
    read only = No

[FinanceDocs]
    comment = Finance Department
    path = /mnt/discn/compar/finance
    valid users = @Finance, @"Domain Admins"
    read only = no
    browseable = yes
    create mask = 0660
    directory mask = 0770

[HRDocs]
    comment = HR Department
    path = /mnt/discn/compar/hr
    valid users = @HR_Staff, @"Domain Admins"
    read only = no
    browseable = yes
    create mask = 0660
    directory mask = 0770

[Public]
    comment = Public
    path = /mnt/discn/compar/public
    read only = yes
    browseable = yes
```

## Step 10 — Check the Syntax

We validate the Samba configuration syntax:

```bash
testparm
```

## Step 11 — Reload Samba and List Shares

We reload the Samba service and list the available shares to verify:

```bash
sudo systemctl reload samba-ad-dc
smbclient -L localhost -U administrator
```

## Step 12 — Configure ACLs

### For FinanceDocs

```bash
sudo setfacl -m "g:LAB05\\finance:rwx" /mnt/data/shares/finance
sudo setfacl -d -m "g:LAB05\\finance:rwx" /mnt/data/shares/finance
sudo chmod +t /mnt/data/shares/finance
```

### For HRDocs

```bash
sudo setfacl -m "g:LAB05\\hr_staff:rwx" /mnt/data/shares/hr
sudo setfacl -d -m "g:LAB05\\hr_staff:rwx" /mnt/data/shares/hr
```

### For Public

```bash
sudo setfacl -m "g:LAB05\\domain users:rx" /mnt/data/shares/public
sudo setfacl -d -m "g:LAB05\\domain users:rx" /mnt/data/shares/public
```

## Step 13 — Verify ACLs

```bash
getfacl /mnt/data/shares/finance
getfacl /mnt/data/shares/hr
getfacl /mnt/data/shares/public
```

## Step 14 — Create the Backup Script

We create an automated backup script at `/usr/local/bin/backup_shares.sh`:

```bash
#!/bin/bash
# Automated backup for Samba shared folders
BACKUP_DIR="/backup/samba"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/samba_backup_$DATE.tar.gz" -C /mnt/discn compar

# Keep only last 7 backups
cd "$BACKUP_DIR" && ls -t *.tar.gz 2>/dev/null | tail -n +8 | xargs -r rm

echo "Backup completed: $DATE" >> "$BACKUP_DIR/backup.log"
```

## Step 15 — Schedule the Backup with Crontab

We schedule the backup script to run daily at 19:00 using crontab:

```bash
sudo crontab -l
```

Crontab entry:

```
0 19 * * * /usr/local/bin/backup_shares.sh >> /var/log/samba_backup.log 2>&1
```
