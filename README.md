# Installing Proxmox on an Asus PN64
<img width="400" alt="" src="https://github.com/rafaelurrutiasilva/Proxmox_on_Nuc/blob/main/Images/Proxmox_on_nuc_smaller.png?raw=true" allign=left><br>

<!-- **Installing Proxmox on an Asus PN64** -->
<br>**Authors:** _<a href="https://github.com/Filipanderssondev">Filip Andersson</a> and <a href="https://github.com/JonatanHogild">Jonatan Högild</a>_<br>
Publiceringsdatum<br>

## Abstract
First project <a href="https://github.com/rafaelurrutiasilva/Proxmox_on_Nuc/blob/main/Extra/Mermaid/Projects.md">in a series of projects</a> during our internship at **The Swedish Meteorological and Hydrological Institute** [(SMHI)](https://www.smhi.se/en/about-smhi), Installation of Proxmox on an Asus PN64 ax210NGW (NUC/Mini-PC). <br>
<br>

## Table of Contents
1. [Introduction](#1-introduction)
2. [Goals and Objectives](#2-goals-and-objectives)
3. [Method](#3-method)<br>
   3.1 [Installation](#31-installation)<br>
   3.2 [Network Configuration](#32-network-configuration)<br>
4. [Target Audience](#4-Target-Audience)
5. [Document Status](#5-document-status)
6. [Disclaimer](#6-disclaimer)
7. [Scope and Limitations](#7-scope-and-limitations)<br>
   7.1 [Scope](#71-Scope)<br>
   7.2 [Limitations](#72-Limitations)
8. [Environment](#8-environment)<br>
   8.1 [Hardware](#81-hardware)<br>
   8.2 [Software](#82-software)
9. [Acknowledgments](#9-acknowledgments)
10. [References](#10-references)
11. [Conclusion](##conclusion)

<br>

## 1. Introduction<br>
**Greetings!**
_...and welcome to our project. This project is a the first project <a href="https://github.com/rafaelurrutiasilva/Proxmox_on_Nuc/blob/main/Extra/Mermaid/Projects.md">in a series of projects</a> with the end goal of setting up a complete virtualized, automated, and monitored IT-Enviroment as a part of our internship on [The Swedish Meteorological and Hydrological Institute (SMHI)](https://www.smhi.se/en/about-smhi) IT-department at the headquarters in Norrköping. The second goal of these projects are also supposed to serve as a set-up guide here on Github for anyone and everyone that wants to follow along! we will link every project to each other aswell._<br>

**<a href="https://github.com/Filipanderssondev">Filip Andersson</a> and <a href="https://github.com/JonatanHogild">Jonatan Högild</a>**
<!-- Inledning - Bakgrund och syfte. Eventuell översiktbild här -->
<br>

## 2. Goals and Objectives
This is part of a larger ongoing Infrastructure as Code (IaC) that will use Proxmox as a base. 
The goal of this project is to build a complete IT-environment and gain a deeper understanding of the underlying components and their part in a larger production chain.
<br>
<br>

## 3. Method

### 3.1 Installation
- 3.1.1 Proxmox VE 9.1 was downloaded from the <a href=https://proxmox.com/en/downloads/proxmox-virtual-environment/iso>official site</a>.

- 3.1.2 A SHA256 checksum is provided for each .ISO. This hash can be confirmed on Windows using powershell: <pre>Get-FileHash .\proxmox-ve_9.0.1.iso -Algorithm SHA 256</pre>

- 3.1.3 Burn the .ISO file to a USB-stick using <a href=https://rufus.ie>rufus</a>.

- 3.1.4 Plug in the USB to the Asus machine and enter the UEFI settings. Configure the following:
   - Secure Boot disabled
   - Intel VT-x enabled
   - Intel VT-d enabled
   - Change boot order to begin with the USB-stick.

- 3.1.5 Save the changes and restart, then follow the installation guide.
The installation is fairly straight-forward, but there are some important things to consider.

- 3.1.6 There are multiple file-system choices, each providing its own strengths and weaknesses.
For this project, we chose to go with ext4, as it seem most approriate for our small lab environment. 
We also chose to to add 10 GB swap space. 

- 3.1.7 Once installed, the system will reboot into a CLI. Enter root as user and log in. 
<br>
<br>

### 3.2 Network Configuration
- 3.2.1 Network configuration is found in **/etc/network/interfaces** and should look like this:
   <pre>
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
   </pre>
<br>

> After installation, we were given a network segment to lab on within our enterprise network. However, we had issues reaching our default gateway, leading to a debugging session trying to locate the error by the method of elimination in the OSI layers.
> we examined various things on Debian for potential solutions. Doing a packet capture on the port, we could tell that ARP requests were being sent out, but no replies were recivied.
> Not having access to the switches here, we got help from the network guys and after some more debugging, we were able to determine that the SVI was not properly configured.

- 3.2.2 SSH<br>
The Debian base (Trixie/13) comes with SSH preinstalled. Check SSH connectivity with:
<pre>ssh user@ip</pre>

- 3.2.3 Add Users<br>
We added two new users for ourselves with:
<pre>adduser jonatan
adduser filip</pre>

At some point later, we will also include ourselves in the sudo group. But right now sudo isn't installed on the system.

- 3.2.4 Updating Debian and Proxmox<br>
Our server does not have full access to the Internet. We request resources over the Internet, and create an errand to the network group. So before we can update the system, we request access to the domain-names in question.
Debian stores URIs from which it recivies updates in /etc/apt/sources.list by default. Proxmox rearanges this slightly, and have 3 main repositories. 

<!--
- 3.2.3. Test Internet connectivity with: <pre>ping 8.8.8.8</pre>
<br><br>

- 3.2.4. Log into the web GUI in a browser using your own ip address: <pre>https://xxx.xxx.xxx.xxx:8006/</pre>
>-->
<br>

## 4. Target Audience
- This repo is for anyone who wants a step-by-step guide on installing Proxmox VE.
This repo is also part of a larger project aimed at people interested in learning about IaC, and building such an environment from scratch. 
<br><br>

## 5. Document Status
> [!NOTE]  
> This is a work in progress.<br>
> This repo is part of a larger ongoing project.
<br>

## 6. Disclaimer
> [!CAUTION]
> This is intended for learning, testing, and experimentation. The emphasis is not on security or creating an operational environment suitable for production.
<br>

## 7. Scope and Limitations
- ### 7.1. Scope
   * Step-by-step instructions for installing Proxmox VE.
   * Basic post-installation configuration.

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
- We would like to thank <a href=https://github.com/rafaelurrutiasilva>Rafael Urrutia</a> for his continuous support and guidance, the skilled network technichians <a href=https://github.com/robertbrokull>Robert Brokull</a>, <a href=https://github.com/marcusjehrlander>Marcus Jehrlander</a>, Martin Lennartsson, <a href="https://github.com/kd00r">Patrik</a> and the ITI team at SMHI Norrköping. 
<br><br>

## 10. References
- [Proxmox Requirements](https://www.proxmox.com/en/products/proxmox-virtual-environment/requirements)
- [Proxmox ISO file download](https://proxmox.com/en/downloads/proxmox-virtual-environment/iso)
- [The ASUS device we used](https://www.asus.com/displays-desktops/mini-pcs/pn-series/asus-expertcenter-pn64/techspec/)
- [Rufus software for burning the ISO image](https://rufus.ie/en/)
<br>

## 11. Conclusion
- The aim of this project was building a solid foundation for our IT-enviroment, and we feel confident to say that we accomplished that. This was a fun and challenging project, both technical and challenging in other aspects, like agile communication due to diffrent backgrounds and experiences but we always managed to succeed in the in those aspects aswell. 
