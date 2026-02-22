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

