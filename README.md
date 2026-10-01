# Wazuh
Sure — here is the **entire Markdown file in one copy box**. You can copy it directly and save it as `wazuh-ubuntu-install.md`.

````
# Wazuh Installation on Ubuntu Server

## Server Specifications

This guide is for a single-server Wazuh installation.

| Resource | Specification |
|---|---|
| OS | Ubuntu Server 22.04 / 24.04 LTS |
| CPU | 4 cores |
| RAM | 8 GB |
| Disk | 50 GB+ recommended |
| Architecture | AMD64 / x86_64 |
| Installation | Wazuh All-in-One |

The server will contain:

- Wazuh Manager
- Wazuh Indexer
- Wazuh Dashboard
- Filebeat

---

# 1. Update Ubuntu

Login to the Ubuntu server.

Become root:

```bash
sudo -i
````

 Update the system:

```
apt update
apt upgrade -y
```

 Install basic packages:

```
apt install -y curl wget gnupg apt-transport-https unzip vim net-tools
```

 Reboot:

```
reboot
```

 Reconnect after the reboot:

```
ssh user@SERVER_IP
```

 Become root again:

```
sudo -i
```

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
hostname -I
```

 Example:

```
192.168.1.50
```

 Write down this IP.

 You will use it later to access the Wazuh Dashboard.

---

 # 5\. Configure /etc/hosts

 Edit the hosts file:

```
nano /etc/hosts
```

 Add your server IP:

```
192.168.1.50 wazuh-server
```

 Replace `192.168.1.50` with your actual IP.

 Save:

```
CTRL + O
ENTER
CTRL + X
```

 Test:

```
ping -c 2 wazuh-server
```

---

 # 6\. Install Wazuh All-in-One

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

 # 14\. Configure Firewall

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

 # 16\. Verify Dashboard

 After login, you should see the Wazuh Dashboard.

 You should be able to access sections such as:

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

 At this point the Wazuh server is working.

---

 # 17\. Check Wazuh Logs

 Check Wazuh Manager logs:

```
tail -100 /var/ossec/logs/ossec.log
```

 Follow logs live:

```
tail -f /var/ossec/logs/ossec.log
```

 Stop with:

```
CTRL + C
```

---

 # 18\. Check Wazuh Manager Logs

```
journalctl -u wazuh-manager -n 100 --no-pager
```

 Live logs:

```
journalctl -u wazuh-manager -f
```

 Stop with:

```
CTRL + C
```

---

 # 19\. Check Wazuh Indexer Logs

```
journalctl -u wazuh-indexer -n 100 --no-pager
```

 Live logs:

```
journalctl -u wazuh-indexer -f
```

---

 # 20\. Check Wazuh Dashboard Logs

```
journalctl -u wazuh-dashboard -n 100 --no-pager
```

 Live logs:

```
journalctl -u wazuh-dashboard -f
```

---

 # 21\. Check Filebeat Logs

```
journalctl -u filebeat -n 100 --no-pager
```

 Live logs:

```
journalctl -u filebeat -f
```

---

 # 22\. Check RAM

 Because the server has 8 GB RAM, monitor memory usage.

 Run:

```
free -h
```

 Install htop:

```
apt install -y htop
```

 Run:

```
htop
```

---

 # 23\. Check Disk

 Run:

```
df -h
```

 Check Wazuh storage:

```
du -sh /var/ossec
```

 Keep an eye on disk usage because Wazuh stores security event data.

---

 # 24\. Check CPU

 Run:

```
nproc
```

 Detailed information:

```
lscpu
```

 Monitor CPU:

```
top
```

---

 # 25\. Test Dashboard From Another Linux Machine

 Run:

```
curl -k https://192.168.1.50
```

 Replace the IP with your Wazuh server IP.

---

 # 26\. Test Dashboard From Windows

 Open PowerShell:

```
Test-NetConnection 192.168.1.50 -Port 443
```

 Expected:

```
TcpTestSucceeded : True
```

---

 # 27\. Test Wazuh Agent Port

 From another Linux machine:

```
nc -zv 192.168.1.50 1514
```

 Test enrollment:

```
nc -zv 192.168.1.50 1515
```

 If `nc` is not installed:

```
apt install -y netcat-openbsd
```

---

 # 28\. Check Wazuh Agents

 Run:

```
/var/ossec/bin/agent_control -l
```

 If you have not installed any agents yet, there may be no agents listed.

 That is normal.

---

 # 29\. Install Your First Wazuh Agent

 After the Wazuh server is working, install Wazuh agents on machines you want to monitor.

 Examples:

```
Windows Server
Windows PC
Ubuntu Server
Debian Server
Web Server
Database Server
Linux Server
```

 The Wazuh server should normally be centralized.

 Architecture:

```
                         Wazuh Dashboard
                              |
                              |
                              v
                       Wazuh Manager
                              |
                              |
                              v
                       Wazuh Indexer
                              |
               +--------------+--------------+
               |              |              |
               v              v              v
           Windows         Ubuntu         Linux
            Agent           Agent          Agent
```

---

 # 30\. Get Agent Installation Instructions

 Login to:

```
https://YOUR_WAZUH_SERVER_IP
```

 Go to the Agents section.

 Choose:

```
Deploy new agent
```

 Select:

```
Operating System
```

 Select the operating system of the machine you want to monitor.

 The Dashboard will provide the appropriate agent installation commands.

---

 # 31\. Check Agents After Installation

 Run:

```
/var/ossec/bin/agent_control -l
```

 You should see the enrolled agent.

 You can also see the agent in:

```
Wazuh Dashboard
    >
Agents
```

---

 # 32\. Restart Wazuh Services

 Restart Manager:

```
systemctl restart wazuh-manager
```

 Restart Indexer:

```
systemctl restart wazuh-indexer
```

 Restart Dashboard:

```
systemctl restart wazuh-dashboard
```

 Restart Filebeat:

```
systemctl restart filebeat
```

 Check all services:

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

 # 33\. Reboot Test

 Test that Wazuh starts automatically after reboot.

 Run:

```
reboot
```

 Wait for the server to come back.

 Reconnect:

```
ssh user@YOUR_WAZUH_SERVER_IP
```

 Become root:

```
sudo -i
```

 Check:

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

 Open the Dashboard:

```
https://YOUR_WAZUH_SERVER_IP
```

---

 # 34\. Troubleshooting Dashboard

 If the Dashboard does not open:

 Check:

```
systemctl status wazuh-dashboard
```

 Check port 443:

```
ss -lntp | grep 443
```

 Check logs:

```
journalctl -u wazuh-dashboard -n 200 --no-pager
```

 Check firewall:

```
ufw status
```

 Allow HTTPS:

```
ufw allow 443/tcp
ufw reload
```

 Try again:

```
https://YOUR_WAZUH_SERVER_IP
```

---

 # 35\. Troubleshooting Wazuh Indexer

 Check:

```
systemctl status wazuh-indexer
```

 Logs:

```
journalctl -u wazuh-indexer -n 200 --no-pager
```

 Check port:

```
ss -lntp | grep 9200
```

 Check memory:

```
free -h
```

 Check disk:

```
df -h
```

---

 # 36\. Troubleshooting Wazuh Manager

 Check:

```
systemctl status wazuh-manager
```

 Logs:

```
journalctl -u wazuh-manager -n 200 --no-pager
```

 Wazuh log:

```
tail -200 /var/ossec/logs/ossec.log
```

---

 # 37\. Troubleshooting Filebeat

 Check:

```
systemctl status filebeat
```

 Logs:

```
journalctl -u filebeat -n 200 --no-pager
```

---

 # 38\. If RAM Usage Is High

 Check:

```
free -h
```

 Find high-memory processes:

```
ps aux --sort=-%mem | head -15
```

 Use:

```
htop
```

 An 8 GB server is appropriate for a small Wazuh deployment, but resource requirements increase with:

 - Number of agents
- Number of events
- Log volume
- Retention period
- Enabled Wazuh modules

---

 # 39\. If Disk Usage Is High

 Check:

```
df -h
```

 Check Wazuh data:

```
du -sh /var/ossec
```

 Find large directories:

```
du -xh /var | sort -h | tail -20
```

 Do not delete Wazuh files manually unless you know exactly what they contain.

---

 # 40\. Final Health Check

 Run:

```
echo "===== WAZUH MANAGER ====="
systemctl is-active wazuh-manager

echo "===== WAZUH INDEXER ====="
systemctl is-active wazuh-indexer

echo "===== WAZUH DASHBOARD ====="
systemctl is-active wazuh-dashboard

echo "===== FILEBEAT ====="
systemctl is-active filebeat

echo "===== CPU ====="
nproc

echo "===== MEMORY ====="
free -h

echo "===== DISK ====="
df -h /

echo "===== PORTS ====="
ss -lntp | grep -E '443|1514|1515|55000|9200'
```

 Expected:

```
===== WAZUH MANAGER =====
active

===== WAZUH INDEXER =====
active

===== WAZUH DASHBOARD =====
active

===== FILEBEAT =====
active
```

---

 # 41\. Final Checklist

```
[✓] Ubuntu Server installed
[✓] 4 CPU cores
[✓] 8 GB RAM
[✓] Disk available
[✓] Server updated
[✓] Hostname configured
[✓] IP address configured
[✓] Wazuh installed
[✓] Wazuh Manager running
[✓] Wazuh Indexer running
[✓] Wazuh Dashboard running
[✓] Filebeat running
[✓] Firewall configured
[✓] Dashboard accessible
[✓] Admin login working
[✓] Reboot tested
[ ] Wazuh agents installed
[ ] Agents connected
[ ] Security events verified
```

---

 # 42\. Final Dashboard URL

 After installation, access:

```
https://YOUR_WAZUH_SERVER_IP
```

 Example:

```
https://192.168.1.50
```

 Login:

```
Username: admin
Password: The password shown by the Wazuh installer
```

---

 # 43\. Official Wazuh Documentation

 Wazuh Quickstart:

 https://documentation.wazuh.com/current/quickstart.html

 Wazuh Installation Guide:

 https://documentation.wazuh.com/current/installation-guide/index.html

 Wazuh Server Installation:

 https://documentation.wazuh.com/current/installation-guide/wazuh-server/index.html

 Wazuh Dashboard:

 https://documentation.wazuh.com/current/installation-guide/wazuh-dashboard/index.html

---

 # 44\. Installation Summary

 The main installation commands are:

```
sudo -i

apt update
apt upgrade -y

apt install -y curl wget gnupg apt-transport-https unzip vim net-tools

hostnamectl set-hostname wazuh-server

curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh

bash wazuh-install.sh -a
```

 Then verify:

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

 Finally open:

```
https://YOUR_WAZUH_SERVER_IP
```

 Your Wazuh all-in-one server should now be ready.

```

```
