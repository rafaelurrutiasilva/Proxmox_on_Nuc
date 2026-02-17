# Installing Proxmox on an Asus PN64
<img width="400" alt="" src="https://github.com/rafaelurrutiasilva/Proxmox_on_Nuc/blob/main/Images/Proxmox_on_nuc_smaller.png?raw=true" allign=left><br>

<!-- **Installing Proxmox on an Asus PN64** -->
<br>**Authors:** _<a href="https://github.com/Filipanderssondev">Filip Andersson</a> and <a href="https://github.com/JonatanHogild">Jonatan Högild</a>_<br>

01-12-2025<br>

## Abstract
First project <a href="https://github.com/rafaelurrutiasilva/Proxmox_on_Nuc/blob/main/Extra/Mermaid/Projects.md">in a series of projects</a> during our internship at **The Swedish Meteorological and Hydrological Institute** [(SMHI)](https://www.smhi.se/en/about-smhi), Installation of Proxmox on an Asus PN64 ax210NGW (NUC/Mini-PC). <br>
<br>

## Table of Contents
1. [Introduction](#1-introduction)
2. [Goals and Objectives](#2-goals-and-objectives)
3. [Method](#3-method)<br>
   3.1 [Installation](#31-installation)<br>
   3.2 [Network Configuration](#32-network-configuration)<br>
   3.3 [SSH](#33-ssh)<br>
   3.4 [Proxmox web GUI](#34-proxmox-web-gui)<br>
   3.5 [Add users](#35-add-users)<br>
   3.6 [NTP server](#36-ntp-server)<br>
   3.7 [Obtain an SSL certificate](#37-obtain-an-ssl-certificate)<br>
4. [Target Audience](#4-target-audience)
5. [Document Status](#5-document-status)
6. [Disclaimer](#6-disclaimer)
7. [Scope and Limitations](#7-scope-and-limitations)
8. [Environment](#8-environment)
9. [Acknowledgments](#9-acknowledgments)
10. [References](#10-references)
11. [Conclusion](#11-conclusion)


## 1. Introduction<br>
**Greetings!**
...and welcome to our project! This project is the first project <a href="https://github.com/rafaelurrutiasilva/Proxmox_on_Nuc/blob/main/Extra/Mermaid/Projects.md">in a series of projects</a> with the end goal of setting up a complete virtualized, automated, and monitored IT-Enviroment as a part of our internship on [The Swedish Meteorological and Hydrological Institute (SMHI)](https://www.smhi.se/en/about-smhi) IT-department at the headquarters in Norrköping. The second goal of these projects are also supposed to serve as a set-up guide here on Github for anyone and everyone that wants to follow along!<br>

Other projects in series:<br>
Part 2 - [Rocky Linux golden image for cloning](https://github.com/Filipanderssondev/Rocky_Linux_OS_Base_for_VMs)<br>
Part 3 - [Ansible on management VM](https://github.com/JonatanHogild/Ansible_on_management_vm)

## 2. Goals and Objectives
This is part of a larger ongoing IT-infrastructure project that will use Proxmox as a base. 
The goal of this project is to build a complete IT-environment and gain a deeper understanding of the underlying components and their part in a larger production chain.

## 3. Method

### 3.1 Installation

 #### 3.1.1 Download and prepare Proxmox
 
 Proxmox VE 9.1 can be downloaded from the <a href=https://proxmox.com/en/downloads/proxmox-virtual-environment/iso>official site</a>.

A SHA256 checksum is provided for each .ISO. This hash can be confirmed on Windows using powershell: 
```
Get-FileHash .\proxmox-ve_9.0.1.iso -Algorithm SHA 256
```

Burn the .ISO file to a USB-stick using <a href=https://rufus.ie>rufus</a>, or some other imaging utility. 

#### 3.1.2 UEFI settings

Plug in the USB to the Asus machine and enter the UEFI settings. Configure the following:
   - Secure Boot disabled
   - Intel VT-x enabled
   - Intel VT-d enabled
   - Change boot order to begin with the USB-stick.

Save the changes and restart.

#### 3.1.3 installation

Follow the installation guide.
The installation is fairly straight-forward, but there are some important things to consider.

There are multiple file-system choices, each providing its own strengths and weaknesses. ZFS (Zetabyte File System) offers advanced features like snapchats, replication, self-healing, and more. ZFS also consumes a lot of RAM and is write-intensive. For this project, we chose to go with Ext4 (Extended file system 4), as it seem most approriate for our small lab environment, and the resources we have available. 

We also chose to to add 10 GB swap space.

Once installed, the system will reboot into a CLI. Enter root as user and log in. 
<br>

### 3.2 Network configuration

Networking can be configured during installation, or afterwards.

Network configuration is found in */etc/network/interfaces* and should look like this:
```
   auto lo
   iface lo inet loopback

   iface enp2s0 inet manual

   auto vmbr0
   iface vmbr0 inet static
   address xxx.xxx.xxx.xxx/xx
   gateway xxx.xxx.xxx.xxx
   bridge_ports enp2s0
   bridge_stp off
   bridge_fd 0
```

> We have been given our own network segment within a broader enterprise network. As such, special considerations will be made from time to time, and we will need to send requests for resources over the Internet before we can access them. These things will not apply to a home-lab, and so we will omit these details when redundant. 

### 3.3 SSH

SSH (Secure SHell) permits remote access to the server. The Debian base (Trixie/13) comes with SSH preinstalled and SSHD running. 

Check SSH connectivity with:
```
ssh user@ip-address
```

Or:
```
ssh user@hostname
```

When connecting for the first time, SSH will warn you that the authenticity of host can't be established. This is normal, type *yes* to continue.

At this point, we can detach peripherals from the server and leave it headless. 

### 3.4 Proxmox web GUI

#### 3.4.1 Connect to the Proxmox web GUI

Connect to the web GUI in a browser using the server's ip address and the Proxmox-specific port (8006): 
```
https://xxx.xxx.xxx.xxx:8006/
```

If a warning about http not being secure shows up, ignore it and proceed. 

You will be prompted to log in with a username and password. There are two different methods of logging in, either with a Linux user (Linux PAM), or with the Proxmox VE authentication server. log in with the root user that was created during installation. 

#### 3.4.2 Change Proxmox Repositories

Proxmox uses two different repositories for updates, an enterprise repo and a no-subscription repo. This project will use the no-subscription repo.

Go to Updates > Repositories
Add a new repository, select *No-Subscription*

Disable the *pve-enterprise* and *ceph-squid* repositories.

#### 3.4.3 Update the system

Go into Updates, refresh and upgrade. Reboot the system if prompted.

### 3.5 Add users

It can be a good idea to add human user accounts, and refrain from just using the root user.

#### 3.5.1 Add Users

Go into the Debian shell and add new users:
```
adduser jonatan
adduser filip
```

#### 3.5.2 Add Sudo

Install sudo with:
```
apt install sudo
```

Then add users to the sudo group: 
```
usermod -aG sudo jonatan
usermod -aG sudo filip
```

### 3.6 NTP server

Proxmox uses a predefined pool of NTP servers to synchronize time. If this works for you, skip this step. We'll be using a local NTP server instead. 

#### 3.6.1 chrony.conf

Open the chrony.conf config-file:
```
sudo nano /etc/chrony/chrony.conf
```

comment out the line *pool 2.debian.pool.ntp.org iburst* and add *server <ip-address/domain.name> iburst*. 

Save and exit, then restart chrony with:
```
systemctl restart chronyd
```

### 3.7 Obtain an SSL certificate

Proxmox automatically generates a self-signed certificate, but we will instead use a certificate from our own organisation. This process will be different depending on where the certificate is acquired from. 

#### 3.7.1 Generate CSR and private key

A CSR (Certificate Signing Request) can be created with this command:
```
openssl req -out CSR.csr -new -newkey rsa:2048 -nodes -keyout privateKey.key
```

This command lets you fill in various fields which will be used to generate *CSR.csr* and *privateKey.key*. 

#### 3.7.2 Create a certificate chain

Once created, the CSR can be sent to our organisation for processing. Once validated and enrolled, we receive the .crt file with mail. 

We also download the Root CA and the issuing enterprise CA and string them together in the following order: Server > Issuing Enterprise > Root:
```
cat server.crt issuingenterprise.crt root.crt > chain.txt
```
#### 3.7.3 Upload certifictae

In the Proxmox web-gui, Go to > Certificates > Upload Custom Certificate

Add the private key file and the certificate chain file, upload and reload the web interface. If succesful, you may also log into the web ui with any of the provided subject alternative names (SALs).

## 4. Target Audience
This repo is for anyone who wants a step-by-step guide on installing Proxmox VE and preparing it for hosting a virtual environment. 
This repo is also part of a larger project aimed at people interested in learning about IT infrastructure, and building such an environment from scratch. 


## 5. Document Status
This repo is completed.
This repo is part of a larger ongoing project.

## 6. Disclaimer
> [!CAUTION]
> This is intended for learning, testing, and experimentation. The emphasis is not on security or creating an operational environment suitable for production.
<br>

## 7. Scope and Limitations
- ### 7.1. Scope
  Step-by-step instructions for installing Proxmox VE and post-installation configuration.

- ### 7.2. Limitations
   * This guide is not intended for production-grade, multi-node clusters or advanced HA setups.
   * Hardware compatibility varies; If unsure, check <a href=https://www.proxmox.com/en/products/proxmox-virtual-environment/requirements>hardware requirements</a> before proceeding. 
   * Network configuration is for now limited to a single-node setup and may not apply to complex environments.
   * Instructions may become outdated as software updates; always verify with the official documentation.
<br>

## 8. Environment
- ### 8.1. Hardware
   - Asus PN64 ax210NGW 16 GB (See reference).
   - USB flash drive 64 GB.

- ### 8.2. Software
   - Windows 10 was used for downloading Proxmox.
   - Rufus 3.2 was used for burning the Proxmox .ISO file onto the USB.
   - Proxmox uses a Debian base with a CLI.
<br>

## 9. Acknowledgments
- We would like to thank <a href=https://github.com/rafaelurrutiasilva>Rafael Urrutia</a> for his continuous support and guidance, the skilled network technichians <a href=https://github.com/robertbrokull>Robert Brokull</a>, <a href=https://github.com/marcusjehrlander>Marcus Jehrlander</a>, Martin Lennartsson, <a href="https://github.com/kd00r">Patrik</a> and the ITi team at SMHI Norrköping. 
<br><br>

## 10. References
- [Proxmox Requirements](https://www.proxmox.com/en/products/proxmox-virtual-environment/requirements)
- [Proxmox ISO file download](https://proxmox.com/en/downloads/proxmox-virtual-environment/iso)
- [The ASUS device we used](https://www.asus.com/displays-desktops/mini-pcs/pn-series/asus-expertcenter-pn64/techspec/)
- [Rufus software for burning the ISO image](https://rufus.ie/en/)
<br>

## 11. Conclusion
- The aim of this project was building a solid foundation for our IT-enviroment, and we feel confident to say that we accomplished that. This was a fun and challenging project, both technical and challenging in other aspects, like agile communication due to different backgrounds and experiences but we always managed to succeed in those aspects as well. 
