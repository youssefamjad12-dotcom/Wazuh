# Install Wazuh

 # Wazuh Installation on Ubuntu Server

 ## Server Specifications

 This guide is for a single-server Wazuh installation.

 | Resource | Specification |
| --- | --- |
| OS | Ubuntu Server 22.04 / 24.04 LTS |
| CPU | 4 cores |
| RAM | 8 GB |
| Disk | 50 GB+ recommended |
| Architecture | AMD64 / x86\_64 |
| Installation | Wazuh All-in-One |

The server will contain:

 - Wazuh Manager
- Wazuh Indexer
- Wazuh Dashboard
- Filebeat

---

 # 0\. Prerequisite — Install Ubuntu Server First

 Before starting the Wazuh installation, you must have a working **Ubuntu Server**.

 If you are a student and do not know how to create a VMware virtual machine or install Ubuntu Server, you can use the following detailed guide:

 ## Ubuntu Server Installation on VMware

 [Ubuntu Server Installation Guide](<https://github.com/youssefamjad12-dotcom/Ubunto-server/>)

 This guide explains step-by-step how to:

 - Download the Ubuntu Server ISO
- Create a VMware virtual machine
- Configure the VM CPU
- Configure RAM
- Configure the virtual disk
- Configure the network
- Attach the Ubuntu Server ISO
- Start the virtual machine
- Install Ubuntu Server
- Configure the hostname
- Configure the network
- Create the Ubuntu user
- Install OpenSSH Server
- Reboot Ubuntu
- Check the IP address
- Verify CPU, RAM, disk, network, and SSH

 Follow the Ubuntu Server guide first.

 When Ubuntu Server is successfully installed and you can log in to the server, return to this document and continue with **Step 1**.

---

 # 1\. Required VMware Configuration

 For this Wazuh installation, the Ubuntu Server VM should have approximately the following configuration:

 | Setting | Recommended Value |
| --- | --- |
| VM Name | Wazuh-Server |
| Operating System | Ubuntu Server 24.04 LTS |
| CPU | 4 cores |
| RAM | 8 GB |
| Disk | 100 GB recommended |
| Network | Bridged or NAT |
| Hostname | wazuh-server |
| Architecture | AMD64 / x86\_64 |

Example:

```
VMware
   |
   +-- Wazuh-Server
          |
          +-- Ubuntu Server 24.04 LTS
          |
          +-- CPU: 4 cores
          |
          +-- RAM: 8 GB
          |
          +-- Disk: 100 GB
          |
          +-- Network: Bridged/NAT
```

---

 # 2\. Check Server Resources

 ## Check CPU

 Run:

```
nproc
```

 Expected:

```
4
```

 ## Check RAM

 Run:

```
free -h
```

 You should have approximately:

```
8 GB
```

 ## Check Disk

 Run:

```
df -h
```

 Make sure you have enough free disk space.

 Recommended:

```
50 GB+
```

 ## Check Ubuntu Version

 Run:

```
cat /etc/os-release
```

 You should see Ubuntu Server 22.04 LTS or 24.04 LTS.

---

 # 3\. Set Hostname

 Set the hostname:

```
sudo hostnamectl set-hostname wazuh-server
```

 Check the hostname:

```
hostnamectl
```

 Expected:

```
Static hostname: wazuh-server
```

---

 # 4\. Check Server IP

 Run:

```
ip a
```

 You can also use:

```
hostname -I
```

 Example:

```
192.168.1.50
```

 Write down this IP address.

 You will use it later to access the Wazuh Dashboard.

---

 # 5\. Update Ubuntu

 Update the package list:

```
sudo apt update
```

 Upgrade installed packages:

```
sudo apt upgrade -y
```

 After the update finishes, reboot the server:

```
sudo reboot
```

 Log in again after the server restarts.

---

 # 6\. Become Root

 For the Wazuh installation, become root:

```
sudo -i
```

 Check:

```
whoami
```

 Expected:

```
root
```

---

 # 7\. Install Wazuh All-in-One

 Download the Wazuh installation assistant:

```
curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh
```

 Check that the script was downloaded:

```
ls -lh wazuh-install.sh
```

 Run the all-in-one installation:

```
bash wazuh-install.sh -a
```

 Wait for the installation to finish.

 The all-in-one installation installs:

```
Wazuh Indexer
Wazuh Manager
Wazuh Dashboard
Filebeat
```

 > **Important:** Do not close your SSH session while the installation is running.

 The installation may take several minutes.

---

 # 8\. Save Wazuh Admin Password

 At the end of the installation, Wazuh will display information similar to:

```
INFO: You can access the web interface https://192.168.1.50
INFO: User: admin
INFO: Password: ********
```

 **SAVE THE PASSWORD.**

 You need this password to log in to the Wazuh Dashboard.

 Also save:

```
Dashboard URL
Username
Password
Server IP address
```

 Example:

```
Server IP:
192.168.1.50

Dashboard:
https://192.168.1.50

Username:
admin

Password:
YOUR_WAZUH_ADMIN_PASSWORD
```

---

 # 9\. Check Wazuh Manager

 Run:

```
systemctl status wazuh-manager
```

 You want to see:

```
Active: active (running)
```

 Press:

```
q
```

 to exit.

---

 # 10\. Check Wazuh Indexer

 Run:

```
systemctl status wazuh-indexer
```

 You want to see:

```
Active: active (running)
```

 Press:

```
q
```

 to exit.

---

 # 11\. Check Wazuh Dashboard

 Run:

```
systemctl status wazuh-dashboard
```

 You want to see:

```
Active: active (running)
```

 Press:

```
q
```

 to exit.

---

 # 12\. Check Filebeat

 Run:

```
systemctl status filebeat
```

 You want to see:

```
Active: active (running)
```

 Press:

```
q
```

 to exit.

---

 # 13\. Quick Service Check

 Run all four checks together:

```
systemctl is-active wazuh-manager
systemctl is-active wazuh-indexer
systemctl is-active wazuh-dashboard
systemctl is-active filebeat
```

 Expected:

```
active
active
active
active
```

 If all four show:

```
active
```

 the main Wazuh services are running.

---

 # 14\. Check Wazuh Ports

 Run:

```
ss -lntp
```

 You can specifically check the important Wazuh ports:

```
ss -lntp | grep -E '443|1514|1515|55000|9200'
```

 Important ports:

 | Port | Purpose |
| --- | --- |
| 443 | Wazuh Dashboard |
| 1514 | Wazuh Agent communication |
| 1515 | Wazuh Agent enrollment |
| 55000 | Wazuh API |
| 9200 | Wazuh Indexer |

---

 # 15\. Configure Firewall

 Check UFW:

```
ufw status
```

 If UFW is enabled, allow HTTPS:

```
ufw allow 443/tcp
```

 Allow Wazuh agent communication:

```
ufw allow 1514/tcp
```

 Allow Wazuh agent enrollment:

```
ufw allow 1515/tcp
```

 Reload the firewall:

```
ufw reload
```

 Check the firewall:

```
ufw status
```

 For production environments, restrict these ports to your trusted network whenever possible.

---

 # 16\. Open Wazuh Dashboard

 From your computer, open a web browser.

 Use:

```
https://YOUR_WAZUH_SERVER_IP
```

 Example:

```
https://192.168.1.50
```

 The browser may display a certificate warning.

 This can happen because the default Wazuh installation uses its own certificates.

 Continue to the Wazuh Dashboard.

---

 # 17\. Login to Wazuh Dashboard

 Use:

```
Username:
admin
```

 Use the password generated during the Wazuh installation:

```
Password:
YOUR_WAZUH_ADMIN_PASSWORD
```

 After successful login, you should see the Wazuh Dashboard.

---

 # 18\. Verify Wazuh Dashboard

 Check that the Dashboard is accessible and that you can see the Wazuh interface.

 You should be able to access areas such as:

```
Dashboard
Agents
Security Events
Threat Hunting
Vulnerability Detection
File Integrity Monitoring
Security Configuration Assessment
MITRE ATT&CK
```

---

 # 19\. Final Wazuh Service Check

 Run:

```
systemctl is-active wazuh-manager
systemctl is-active wazuh-indexer
systemctl is-active wazuh-dashboard
systemctl is-active filebeat
```

 Expected:

```
active
active
active
active
```

---

 # 20\. Final Installation Checklist

 Before continuing to the next section, verify:

```
[✓] VMware VM created
[✓] Ubuntu Server installed
[✓] CPU = 4 cores
[✓] RAM = 8 GB
[✓] Disk = 50 GB+
[✓] Internet connection working
[✓] Server IP available
[✓] Hostname = wazuh-server
[✓] Wazuh Manager installed
[✓] Wazuh Indexer installed
[✓] Wazuh Dashboard installed
[✓] Filebeat installed
[✓] Wazuh Manager = active
[✓] Wazuh Indexer = active
[✓] Wazuh Dashboard = active
[✓] Filebeat = active
[✓] Wazuh Dashboard accessible
[✓] Admin login successful
```

---

 # 21\. Wazuh Installation Complete

 Your Wazuh server should now look like this:

```
                    VMware
                       |
                       v
                Wazuh-Server VM
                       |
                       v
              Ubuntu Server
                       |
          +------------+------------+
          |            |            |
          v            v            v
       Wazuh        Wazuh        Wazuh
       Manager      Indexer      Dashboard
          |                         |
          |                         |
          +----------+--------------+
                     |
                     v
                Web Browser
```

 Access the Dashboard using:

```
https://YOUR_WAZUH_SERVER_IP
```

 Example:

```
https://192.168.1.50
```

 # END
