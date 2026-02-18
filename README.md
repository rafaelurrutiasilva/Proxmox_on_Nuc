# Installing Proxmox on an Asus PN64
<img alt="" src="https://github.com/rafaelurrutiasilva/Proxmox_on_Nuc/blob/main/Images/proxmox-on-nuc.png" allign=left><br>

**Installing Proxmox on an Asus PN64** <br>
**Authors:** _<a href="https://github.com/Filipanderssondev">Filip Andersson</a> and <a href="https://github.com/JonatanHogild">Jonatan Högild</a>_
01-12-2025

## Abstract
Installing and configuring Proxmox VE on an ASUS Nuc PN64.

## Table of Contents
1. [Introduction](#introduction)
2. [Goals and Objectives](#goals-and-objectives)
3. [Method](#method)
4. [Target Audience](#target-audience)
5. [Document Status](#document-status)
6. [Disclaimer](#disclaimer)
7. [Scope and Limitations](#scope-and-limitations)
8. [Environment](#environment)
9. [Acknowledgments](#acknowledgments)
10. [Implementation](#implementation)<br>
   10.1 [Installation](#installation)<br>
   10.2 [Network Configuration](#network-configuration)<br>
   10.3 [SSH](#ssh)<br>
   10.4 [Proxmox web GUI](#proxmox-web-gui)<br>
   10.5 [Add users](#add-users)<br>
   10.6 [NTP server](#ntp-server)<br>
   10.7 [Obtain an SSL certificate](#obtain-an-ssl-certificate)<br>
11. [Conclusion](#conclusion)
12. [References](#references) <br>
    12.1 [Other projects in our virtual IT-enviroment](#other-projects-in-our-virtual-it-enviroment)

## Introduction<br>
**Welcome!** <br>
This project is about installing and configuring Proxmox VE on an Asus Nuc PN64. This project is the first project <a href="https://github.com/rafaelurrutiasilva/Proxmox_on_Nuc/blob/main/Extra/Mermaid/Projects.md">in a series of projects</a> with the end goal of setting up a complete virtualized, automated, and monitored IT-Enviroment as a part of our internship at [The Swedish Meteorological and Hydrological Institute (SMHI)](https://www.smhi.se/en/about-smhi) IT-infrastructure and production department. These projects also serve as a set-up guide here on Github for anyone that wants to follow along! <br>

_[Other projects in our virtual IT-enviroment](#other-projects-in-our-virtual-it-enviroment)_

## Goals and Objectives
We will set things up for the virtual environment that we will implement in later projects. This is part of a larger ongoing IT-infrastructure project that will use Proxmox as a base. The end-goal of this project is to build a complete IT-environment and gain a deeper understanding of the underlying components and their part in a larger production chain.

## Method
The Proxmox VE image will be downloaded and burnt onto a USB-stick that will be set as a bootable device to run the installer. Proxmox will be installed on a fresh system, where it will be configured for an enterprise network. Users will be added to the base OS, and Proxmox will be updated with a no-subscription repo, NTP server and SSL certificate.

## Target Audience
This repo is for anyone who wants a step-by-step guide on installing Proxmox VE and preparing it for hosting a virtual environment. 
This repo is also part of a larger project aimed at people interested in learning about IT infrastructure, and building such an environment from scratch. 

## Document Status
> [!NOTE]
> This repo is completed.
> This repo is part of a larger ongoing project.

## Disclaimer
> [!CAUTION]
> This is intended for learning, testing, and experimentation. The emphasis is not on security or creating an operational environment suitable for production.

## Scope and Limitations
* Step-by-step instructions for installing and configuring Proxmox VE. 
* This guide is not intended for production-grade, multi-node clusters or advanced HA setups.
* Hardware compatibility varies; If unsure, check <a href=https://www.proxmox.com/en/products/proxmox-virtual-environment/requirements>hardware requirements</a> before proceeding. 
* Instructions may become outdated as software updates; always verify with the official documentation.
* Sensetive information will be withheld. This will not hinder participation in the guide.

## Environment
 - Asus PN64 ax210NGW
   - Intel® Core™ i7-12700H 
   - 1TB disk
   - 64 GB memory
 - Proxmox VE (9.1.1)

## Acknowledgments
We would like to thank <a href=https://github.com/rafaelurrutiasilva>Rafael Urrutia</a> for his continuous support and guidance, the skilled network technichians <a href=https://github.com/robertbrokull>Robert Brokull</a>, <a href=https://github.com/marcusjehrlander>Marcus Jehrlander</a>, Martin Lennartsson, <a href="https://github.com/kd00r">Patrik</a> and the ITi team at SMHI Norrköping. 

## Implementation

### Installation

 #### Download and prepare Proxmox
 
 Proxmox VE 9.1 can be downloaded from the <a href=https://proxmox.com/en/downloads/proxmox-virtual-environment/iso>official site</a>.

A SHA256 checksum is provided for each .ISO. This hash can be confirmed on Windows using powershell: 
```
Get-FileHash .\proxmox-ve_9.0.1.iso -Algorithm SHA 256
```

Burn the .ISO file to a USB-stick using <a href=https://rufus.ie>rufus</a>, or some other imaging utility. 

#### UEFI settings

Plug in the USB to the Asus machine and enter the UEFI settings. Configure the following:
   - Secure Boot disabled
   - Intel VT-x enabled
   - Intel VT-d enabled
   - Change boot order to begin with the USB-stick.

Save the changes and restart.

> [!CAUTION]
> Be aware that by installing Proxmox VE as intended in this guide, all other files on the system will be wiped. 

Follow the installation guide.
The installation is fairly straight-forward, but there are some important things to consider.

There are multiple file-system choices, each providing its own strengths and weaknesses. ZFS (Zetabyte File System) offers advanced features like snapchats, replication, self-healing, and more. ZFS also consumes a lot of RAM and is write-intensive. For this project, we chose to go with Ext4 (Extended file system 4), as it seem most approriate for our small lab environment, and the resources we have available. 

We also chose to to add 10 GB swap space.

Once installed, the system will reboot into a CLI. Enter root as user and log in. 
<br>

### Network configuration

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

### SSH

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

### Proxmox web GUI

#### Connect to the Proxmox web GUI

Connect to the web GUI in a browser using the server's ip address and the Proxmox-specific port (8006): 
```
https://xxx.xxx.xxx.xxx:8006/
```

If a warning about http not being secure shows up, ignore it and proceed. 

You will be prompted to log in with a username and password. There are two different methods of logging in, either with a Linux user (Linux PAM), or with the Proxmox VE authentication server. log in with the root user that was created during installation. 

#### Change Proxmox Repositories

Proxmox uses two different repositories for updates, an enterprise repo and a no-subscription repo. This project will use the no-subscription repo.

Go to Updates > Repositories
Add a new repository, select *No-Subscription*

Disable the *pve-enterprise* and *ceph-squid* repositories.

#### Update the system

Go into Updates, refresh and upgrade. Reboot the system if prompted.


#### Add Users
It can be a good idea to add human user accounts, and refrain from just using the root user.
Go into the Debian shell and add new users:
```
adduser jonatan
adduser filip
```

*adduser* is prefered over *useradd* on Debian-based distros, since it will prompt the user for information and a password, as well as create a home folder by default. 


#### Add Sudo

Install sudo with:
```
apt install sudo
```

Then add users to the sudo group: 
```
usermod -aG sudo jonatan
usermod -aG sudo filip
```

### NTP server

Proxmox uses a predefined pool of NTP servers to synchronize time. If this works for you, skip this step. We'll be using our organisations NTP-server. 

Open the chrony.conf config-file:
```
sudo nano /etc/chrony/chrony.conf
```

comment out the line *pool 2.debian.pool.ntp.org iburst* and add *server <ip-address/domain.name> iburst*. 

Save and exit, then restart chrony with:
```
systemctl restart chronyd
```

### Obtain an SSL certificate

Proxmox automatically generates a self-signed certificate, but we will instead use a certificate from our own organisation. This process will be different depending on where the certificate is acquired from, so the details provided here won't  apply to everyone.

#### Generate CSR and private key

A CSR (Certificate Signing Request) can be created with this command:
```
openssl req -out CSR.csr -new -newkey rsa:2048 -nodes -keyout privateKey.key
```

This command lets you fill in various fields which will be used to generate *CSR.csr* and *privateKey.key*. 

#### Create a certificate chain

Once created, the CSR can be sent to our organisation for processing. Once validated and enrolled, we receive the .crt file with mail. 

We also download the Root CA and the issuing enterprise CA and string them together in the following order: Server > Issuing Enterprise > Root:
```
cat server.crt issuingenterprise.crt root.crt > chain.txt
```
#### Upload certificate

In the Proxmox web-gui, Go to > Certificates > Upload Custom Certificate

Add the private key file and the certificate chain file, upload and reload the web interface. If succesful, you may also log into the web ui with any of the provided subject alternative names (SALs).

## Conclusion
The aim of this project was building a solid foundation for our virtual IT-enviroment in upcoming projects, and we feel confident to say that we accomplished that. This was a fun project, both technical and challenging in many aspects, like with agile communication due to different backgrounds and experiences but we always managed to succeed in those aspects as well. 

## References
- [Proxmox Requirements](https://www.proxmox.com/en/products/proxmox-virtual-environment/requirements)
- [Proxmox ISO file download](https://proxmox.com/en/downloads/proxmox-virtual-environment/iso)
- [The ASUS device we used](https://www.asus.com/displays-desktops/mini-pcs/pn-series/asus-expertcenter-pn64/techspec/)
- [Rufus software for burning the ISO image](https://rufus.ie/en/)

### Other projects in our virtual IT-enviroment:
- Project 2 - [Rocky Linux golden image for cloning](https://github.com/Filipanderssondev/Rocky_Linux_OS_Base_for_VMs)<br>
- Project 3 - [Ansible on management VM](https://github.com/JonatanHogild/Ansible_on_management_vm)
- Project 4 - [Container stack deployment and monitoring with ansible](https://github.com/Filipanderssondev/Container_Stack_Deployment_With_Ansible)
- Project 5 - [FreeIPA for Virtual Enviroment](https://github.com/JonatanHogild/FreeIPA_for_virtual_environment/)
