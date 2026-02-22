# Linux-ADDS-Documentation
Technical documentation for deploying an ADDS Server on Linux
# SPRINT 1 - Create Domain Controller

## Configure Static IP (172.30.20.0/24)

In this case we edit the network card set to bridge adapter from the installation.

![Network IP Configuration](images/page-01.png)

---

In the installation we also configure the machine name and the username.

Once installed, we will proceed to configure the IP for the internal network card: 10.2.10.254

![Profile configuration and netplan yaml](images/page-02.png)

---

We apply the IP configuration and confirm it has been applied with `ip a`.

To start installing Samba we first run an update, followed by an upgrade.

![Apply netplan, ip a output, apt update, apt upgrade](images/page-03.png)

---

We install Samba.

We rename the Samba configuration file.

We provision the forest.

We copy the generated Kerberos file.

We enable the Active Directory service.

![Samba install, smb.conf rename, domain provision, krb5.conf copy, AD service enable](images/page-04.png)

---

We verify the domain.

First we change the IP on the client machine.

We verify connectivity by pinging the server from the client.

![Domain verification, client IP config](images/page-05.png)

---

We run an nslookup against the domain.

We set the server IP in /etc/resolv.conf.

![Ping to server and nslookup result](images/page-06.png)

---

We ping the FQDN.

We then update the machine.

After updating, we install the following packages.

![resolv.conf, FQDN ping, apt update and upgrade, package install](images/page-07.png)

---

We discover the domain.

We join the domain.

We enable home directory creation.

![realm discover, realm join, pam-auth-update](images/page-08.png)

---

We create users in Samba from the server.

We add groups.

We verify the groups.

We add users to their respective groups.

We verify that the users were created by logging in from the client as one of the users.

![User creation, group creation, group membership, client login verification](images/page-09.png)

---

We create Organizational Units (OUs).

We create groups.

To move already-created users in Samba we can use the following command.

Or we can create them directly with the OU specified.

We check the user structure.

We change the IP on another client machine.

![OU creation, user move, user list, client IP config](images/page-10.png)

---

We verify connectivity by pinging the server from the client.

We run an nslookup against the domain.

![ip a output, netplan yaml, ping to server, nslookup](images/page-11.png)

---

We set the server IP in /etc/resolv.conf.

We ping the FQDN.

We update the machine.

![nslookup, resolv.conf, FQDN ping, apt update/upgrade](images/page-12.png)

---

After updating, we install the following packages.

We discover the domain.

We join the domain.

![apt install, realm discover, realm join](images/page-13.png)

---

We enable home directory creation.

We create users in Samba from the server.

We add groups.

![pam-auth-update, samba user create, group add](images/page-14.png)

---

We verify the groups.

We add users to their respective groups.

We verify that the users were created by logging in from the client.

We create Organizational Units.

We create groups.

To move already-created users in Samba we can use this command.

Or create them directly with the OU specified.

![Group list, user group membership, client login, OU creation, user move](images/page-15.png)

---

We check the user structure.

We change the IP on another client machine.

![User list --full-dn, client IP and netplan yaml](images/page-16.png)

---

We verify connectivity by pinging the server from the client.

We run an nslookup against the domain.

![Ping to server, nslookup](images/page-17.png)

---

We set the server IP in /etc/resolv.conf.

We ping the FQDN.

We update the machine.

After updating, we install the following packages.

![resolv.conf, FQDN ping, apt update/upgrade, apt install](images/page-18.png)

---

We discover the domain.

We join the domain.

We enable home directory creation.

![realm discover, realm join, pam-auth-update](images/page-19.png)

---

We create users in Samba from the server.

We add groups.

We verify the groups.

We add users to their respective groups.

We verify that the users were created by logging in from the client.

![User creation, group creation, group list, user group membership](images/page-20.png)

---

We create Organizational Units.

We create groups.

To move already-created users in Samba we can use this command.

Or create them directly with the OU specified.

We check the user structure.

We change the IP on another client machine.

![OU creation, user move, user list, client IP config](images/page-21.png)

---

We verify connectivity by pinging the server from the client.

We run an nslookup against the domain.

![Ping to server, netplan yaml, nslookup](images/page-22.png)

---

We set the server IP in /etc/resolv.conf.

We ping the FQDN.

We update the machine.

![nslookup, resolv.conf, FQDN ping, apt update/upgrade](images/page-23.png)

---

After updating, we install the following packages.

We discover the domain.

We join the domain.

![apt install, realm discover, realm join](images/page-24.png)

---

We enable home directory creation.

We create users in Samba from the server.

We add groups.

![pam-auth-update, samba user create, group add](images/page-25.png)

---

We verify the groups.

We add users to their respective groups.

We verify that the users were created by logging in from the client.

We create Organizational Units.

We create groups.

To move already-created users in Samba we can use this command.

Or create them directly with the OU specified.

![Group list, user group membership, client login, OU creation, user move](images/page-26.png)

---

We check the user structure.

![User list --full-dn output](images/page-27.png)
