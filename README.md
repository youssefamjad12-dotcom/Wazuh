Install Wazuh
Wazuh Installation on Ubuntu Server
Server Specifications

This guide is for a single-server Wazuh installation.

Resource	Specification
OS	Ubuntu Server 22.04 / 24.04 LTS
CPU	4 cores
RAM	8 GB
Disk	50 GB+ recommended
Architecture	AMD64 / x86_64
Installation	Wazuh All-in-One

The server will contain:

Wazuh Manager

Wazuh Indexer

Wazuh Dashboard

Filebeat

0. Prerequisite — Install Ubuntu Server First

Before starting the Wazuh installation, you must have a working Ubuntu Server.

If you are a student and you do not know how to create a VMware virtual machine or install Ubuntu Server, use the following detailed guide:

Ubuntu Server Installation on VMware:

{"fallbackMarkdown":"Ubuntu Server Installation on VMware — GitHub Repository","reference":{"matched_text":"","prefix":null,"start_idx":1177,"end_idx":1295,"safe_urls":[],"refs":[],"alt":"Ubuntu Server Installation on VMware — GitHub Repository","prompt_text":null,"type":"url","title":"Ubuntu Server Installation on VMware — GitHub Repository","item":{"title":"Ubuntu Server Installation on VMware — GitHub Repository","url":"https://github.com/youssefamjad12-dotcom/Ubunto-server/?utm_source=chatgpt.com","attribution":"github.com","pub_date":null,"snippet":null,"attribution_segments":null,"supporting_websites":[],"refs":[],"hue":null,"attributions":null},"logo":null,"layout":null},"showLoginRequiredCard":false}

The guide explains step-by-step how to:

Download the Ubuntu Server ISO

Create a VMware virtual machine

Configure the VM CPU

Configure 8 GB RAM

Configure the virtual disk

Configure NAT or Bridged networking

Attach the Ubuntu Server ISO

Start the virtual machine

Install Ubuntu Server

Configure the hostname

Configure the network

Create the Ubuntu user

Install OpenSSH Server

Reboot Ubuntu

Check the IP address

Verify CPU, RAM, disk, network, and SSH

Follow that guide until you have successfully booted into Ubuntu Server.

Required Ubuntu VM Configuration

For this Wazuh installation, configure the VMware VM approximately as follows:

VM Name:       Wazuh-Server
Operating OS:  Ubuntu Server 24.04 LTS
CPU:           4 cores
RAM:           8 GB
Disk:          100 GB recommended
Network:       Bridged or NAT
Hostname:      wazuh-server
Architecture:  AMD64 / x86_64


After Ubuntu Server is installed, login to the server and continue with Step 1 below.

1. Update Ubuntu

Login to the Ubuntu server.

Become root:

sudo -i


Update the package list:

apt update


Upgrade installed packages:

apt upgrade -y


Wait until the update finishes.

2. Check Ubuntu Version

Run:

cat /etc/os-release


You should see Ubuntu.

Example:

NAME="Ubuntu"
VERSION="24.04.x LTS"

3. Check CPU

Run:

nproc


Expected:

4

4. Check RAM

Run:

free -h


You should have approximately:

8 GB

5. Check Disk

Run:

df -h


Make sure enough disk space is available.

Recommended:

50 GB+

6. Check Network

Check the IP address:

hostname -I


Example:

192.168.1.50


Check Internet connectivity:

ping -c 4 google.com


If you receive replies, the Internet connection is working.

7. Continue With Wazuh Installation

Once the following are working:

[✓] Ubuntu Server installed
[✓] 4 CPU cores
[✓] 8 GB RAM
[✓] 50 GB+ disk
[✓] Network working
[✓] Internet working
[✓] Ubuntu login working


continue with the Wazuh installation.

Become root if you are not already root:

sudo -i


Then continue with the next Wazuh installation step.

---

 # 2\. Check Server Resources

 Check CPU:

```
nproc
```

 Expected:

```
4
```

 Check RAM:

```
free -h
```

 Check disk:

```
df -h
```

 Check Ubuntu version:

```
cat /etc/os-release
```

---

 # 3\. Set Hostname

 Set the hostname:

```
hostnamectl set-hostname wazuh-server
```

 Check:

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

 Example:

```
192.168.1.50
```

 Write down this IP.

 You will use it later to access the Wazuh Dashboard.

---

 

 # 5\. Install Wazuh All-in-One

 Download the Wazuh installation assistant:

```
curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh
```

 Run the all-in-one installation:

```
bash wazuh-install.sh -a
```

 Wait for the installation to finish.

 This installs:

```
Wazuh Indexer
Wazuh Manager
Wazuh Dashboard
Filebeat
```

 Do not close your SSH session while installation is running.

---

 # 7\. Save Wazuh Admin Password

 At the end of the installation, Wazuh will display information similar to:

```
INFO: You can access the web interface https://192.168.1.50
INFO: User: admin
INFO: Password: ********
```

 SAVE THE PASSWORD.

 You need it to login to the Wazuh Dashboard.

---

 # 8\. Check Wazuh Manager

 Run:

```
systemctl status wazuh-manager
```

 You want:

```
Active: active (running)
```

 Press:

```
q
```

 to exit.

---

 # 9\. Check Wazuh Indexer

 Run:

```
systemctl status wazuh-indexer
```

 You want:

```
Active: active (running)
```

 Press:

```
q
```

 to exit.

---

 # 10\. Check Wazuh Dashboard

 Run:

```
systemctl status wazuh-dashboard
```

 You want:

```
Active: active (running)
```

 Press:

```
q
```

 to exit.

---

 # 11\. Check Filebeat

 Run:

```
systemctl status filebeat
```

 You want:

```
Active: active (running)
```

 Press:

```
q
```

 to exit.

---

 # 12\. Quick Service Check

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

 If all four are `active`, the Wazuh installation is running.

---

 # 13\. Check Wazuh Ports

 Run:

```
ss -lntp
```

 You can specifically check the important ports:

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

 # 14\. Configure Firewall (optional)

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

 Allow agent enrollment:

```
ufw allow 1515/tcp
```

 Reload firewall:

```
ufw reload
```

 Check:

```
ufw status
```

 For production, restrict these ports to your trusted network whenever possible.

---

 # 15\. Open Wazuh Dashboard

 From your computer, open a web browser.

 Use:

```
https://YOUR_WAZUH_SERVER_IP
```

 Example:

```
https://192.168.1.50
```

 The browser may show a certificate warning.

 This can happen with the default Wazuh installation certificates.

 Continue to the Wazuh Dashboard.

 Login with:

```
Username: admin
Password: YOUR_WAZUH_ADMIN_PASSWORD
```

---

