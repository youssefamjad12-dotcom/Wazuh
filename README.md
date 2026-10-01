# Install Wazuh

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

# 0. Prerequisite — Install Ubuntu Server First

Before starting the Wazuh installation, you must have a working **Ubuntu Server**.

If you are a student and you do not know how to create a VMware virtual machine or install Ubuntu Server, you can use the following detailed guide:

## Ubuntu Server Installation on VMware

[Ubuntu Server Installation Guide](https://github.com/youssefamjad12-dotcom/Ubunto-server/)

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

# 1. Required VMware Configuration

For this Wazuh installation, the Ubuntu Server VM should have approximately the following configuration:

| Setting | Recommended Value |
|---|---|
| VM Name | Wazuh-Server |
| Operating System | Ubuntu Server 24.04 LTS |
| CPU | 4 cores |
| RAM | 8 GB |
| Disk | 100 GB recommended |
| Network | Bridged or NAT |
| Hostname | wazuh-server |
| Architecture | AMD64 / x86_64 |

Example:

```text
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
