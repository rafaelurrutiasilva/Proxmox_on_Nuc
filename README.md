# Installing Proxmox on an Asus PN64
<img width="400" alt="" src="https://github.com/rafaelurrutiasilva/Proxmox_on_Nuc/blob/main/Images/Proxmox_on_nuc_smaller.png?raw=true" allign=left><br>
<!-- **Installing Proxmox on an Asus PN64** -->
<br>**Authors:** _Filip Andersson and Jonatan Högild_<br>
Publiceringsdatum<br>

## Abstract
First project in a series of projects during our internship at SMHI (The Swedish Meteorological and Hydrological Institute), Installation of Proxmox on an Asus PN64 ax210NGW (NUC/Mini-PC). <br>
<br>

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
10. [References](#references)
<!-- 11. [Conclusion](#conclusion) -->
<br>

## Introduction<br>
**Greetings!**
_...and welcome to our project. This project is a the first project <a href="[in a series of projects](https://github.com/rafaelurrutiasilva/Proxmox_on_Nuc/blob/main/Extra/Mermaid/Projects.md)" > with the end goal of setting up a complete virtualized, automated, and monitored IT-Enviroment as a part of our internship on SMHI's IT-department (The Swedish Meteorological and Hydrological Institute) headquarters in Norrköping. The second goal of these projects are also supposed to serve as a set-up guide here on Github for anyone and everyone that wants to follow along! we will link every project to each other aswell._<br>

**Filip Andersson and Jonatan Högild**
<!-- Inledning - Bakgrund och syfte. Eventuell översiktbild här -->
<br>

## Goals and Objectives
This is part of a larger ongoing IaC-project (Infrastructure as Code) that will use Proxmox as a base. 
The goal of this project is to build a complete IT-environment and gain a deeper understanding of the underlying components and their part in a larger production chain. <br>
<br><br>

<!-- Structure picture here -->

## Method

### Installation
<br>

1. Proxmox VE 9.1 was downloaded from the <a href=https://proxmox.com/en/downloads/proxmox-virtual-environment/iso>official site</a>.

<br>

2. A SHA256 checksum is provided for each .ISO. This hash can be confirmed on Windows using the powershell command <pre>Get-FileHash .\proxmox-ve_9.0.1.iso -Algorithm SHA 256</pre>
<br>

3. Burn the .ISO file to a USB-stick using <a href=https://rufus.ie>rufus</a>.
<br><br>

4. Plug in the USB to the Asus machine and enter the UEFI settings. Configure the following:
   - Secure Boot disabled
   - Intel VT-x enabled
   - Change boot order to begin with the USB-stick.
<br>

5. Save the changes and restart, then follow the installation instructions:
   - Ext4 will be used for this project.
   - 10 GB swap space was added.
<br><br>

6. Once installed, the system will reboot into a CLI. Enter root as user and log in.
<br><br><br>

### Network Configuration
<br>

7. Network configuration is found in **/etc/network/interfaces** and might look like this:
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

>[!NOTE]
> Potential issue, if you didnt have an issue you can skip to part 8

> Troubleshooting, for general documentation purpose:
Since we’re on an enterprise network, we have different isolated networks within the Network. After installation, we were given a network segment to lab on within our enterprise network.However, we had issues connecting to our default gateway, leading to troubleshooting seasion.
We got help from some of the network technicians in our department who helped us solve this issue, 
so thanks to Robert Brokull, Marcus Jehrlander and Martin Lennartsson. 

To troubleshoot this, like in all areas of IT - breaking it down to smaller pieces (not actually), 
pinpointing the error by the process of elimination and conventional walkthrough of the OSI layers.
- We tried different syntax for the config file, 
- We tried running without the virtual bridge.
- We tried different cables. 
- We thought it could have something to do with Switch port security.
- We checked the ARP table on the server and noted that it was empty.
- We did a packet capture on the interface and the switch and saw that ARP requests was being sent, but no replies were being sent.
- We thought it could have something to do with our port channel not being configured to accept VLAN
- Then at last, it turned out SVI (Switch Virtual Interface) wasnt properly configured, 
and we were told to mention whos fault it was (Ha ha)  but we are just interns so we cant but it wasnt our fault.
<br><br><br>

8. Test Internet connectivity with: <pre>ping 8.8.8.8</pre>
<br><br>

9. Log into the web GUI in a browser using your own ip address: <pre>https://xxx.xxx.xxx.xxx:8006/</pre>
<br><br>

## Target Audience
This repo is for anyone who wants a step-by-step guide on installing Proxmox VE.
This repo is also part of a larger project aimed at people interested in learning about IaC, and building such an environment from scratch. 
<br><br>

## Document Status
> [!NOTE]  
> This is a work in progress.<br>
> This repo is part of a larger ongoing project.
<br>

## Disclaimer
> [!CAUTION]
> This is intended for learning, testing, and experimentation. The emphasis is not on security or creating an operational environment suitable for production.
<br>

## Scope and Limitations
**Scope**
* Step-by-step instructions for installing Proxmox VE.
* Basic post-installation configuration.

**Limitations**
* This guide is not intended for production-grade, multi-node clusters or advanced HA setups.
* Hardware compatibility varies; If unsure, check <a href=https://www.proxmox.com/en/products/proxmox-virtual-environment/requirements>hardware requirements</a> before proceeding. 
* Network configuration is for now limited to a single-node setup and may not apply to complex environments.
* Instructions may become outdated as software updates; always verify with the official documentation.
<br>

## Environment
**Hardware**
- Asus PN64 ax210NGW 16 GB (See reference)
- USB flash drive 64 GB
<br>

**Software**
- Windows 10 was used for downloading Proxmox
- Rufus 3.2 was used for burning the Proxmox .ISO file onto the USB.
- Proxmox uses a Debian base with a CLI.
<br>

## Acknowledgments
We would like to thank <a href=https://github.com/rafaelurrutiasilva>Rafael Urrutia</a> for his continuous support and guidance, And then the skilled network technichians <a href=https://github.com/robertbrokull>Robert Brokull</a>, <a href=https://github.com/marcusjehrlander>Marcus Jehrlander</a>, Martin Lennartsson, <a href="https://github.com/kd00r">Patrik</a> and the ITI team at SMHI Norrköping. 
<br><br>

## References
- [Proxmox Requirements](https://www.proxmox.com/en/products/proxmox-virtual-environment/requirements)
- [Proxmox ISO file download](https://proxmox.com/en/downloads/proxmox-virtual-environment/iso)
- [The ASUS device we used](https://www.asus.com/displays-desktops/mini-pcs/pn-series/asus-expertcenter-pn64/techspec/)
- [Rufus software for burning the ISO image](https://rufus.ie/en/)
<br>

<!--
## Conclusion
Slutsats
-->
