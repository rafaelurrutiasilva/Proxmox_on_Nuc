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

## Introduction<br>
**Greetings!**
_...and welcome to our project. This project is a the first project in a series of projects with the end goal of setting up a complete virtualized, automated, and monitored IT-Enviroment as a part of our internship on SMHI's IT-department (The Swedish Meteorological and Hydrological Institute) headquarters in Norrköping. The second goal of these projects are also supposed to serve as a set-up guide here on Github for anyone and everyone that wants to follow along! we will link every project to each other aswell._<br>

**Filip Andersson and Jonatan Högild**
<!-- Inledning - Bakgrund och syfte. Eventuell översiktbild här -->
<br>

## Goals and Objectives
This is part of a larger ongoing IaC-project (Infrastructure as Code) that will use Proxmox as a base. 
The goal of this project is to build a complete IT-environment and gain a deeper understanding of the underlying components and their part in a larger production chain. <br>
<br><br>

## Method
<br>

### Installation

1. Proxmox VE 9.1 was downloaded from the <a href=https://proxmox.com/en/downloads/proxmox-virtual-environment/iso>official site</a>.

2. A SHA256 checksum is provided for each .ISO. This hash can be confirmed on Windows using the powershell command <pre>Get-FileHash .\proxmox-ve_9.0.1.iso -Algorithm SHA 256</pre>

3. Burn the .ISO file to a USB-stick using <a href=https://rufus.ie>rufus</a>.

4. Plug in the USB to the Asus machine and enter the UEFI settings. Configure the following:
   - Secure Boot disabled
   - Intel VT-x enabled
   - Change boot order to begin with the USB-stick.

5. Save the changes and restart, then follow the installation instructions:
   - Ext4 will be used for this project.
   - 10 GB swap space was added.

6. Once installed, the system will reboot into a CLI. Enter root as user and log in.

### Network Configuration

> [!NOTE]
> If you are trying this on a home network, this isnt relevant to you. However its one issue that might occure if youre trying this on a similar setup and for general documentation purpose - Since we’re in an larger institution/office, we have different isolated networks within the Network.

7. Network configuration is found in **/etc/network/interfaces** and might look like this:
   <pre>
      auto lo
      iface lo inet loopback

      iface enp2s0 inet manual

      auto vmbr0
      iface vmbr0 inet static
      address 10.208.12.20/24
      gateway 10.208.12.1
      bridge_ports enp2s0
      bridge_stp off
      bridge_fd 0
   </pre>

8. Test Internet connectivity with <pre>ping 8.8.8.8</pre>

9. Log into the web GUI in a browser using your own ip address: <pre>https://xxx.xxx.xxx.xxx:8006/</pre>

<!-- 

Dokumentation 25/11



Vad är fel? Steg för steg

- Vi skulle fixa nätverksanslutningsproblemet
Vi kan inte komma åt gateway och inte pinga

Vi har noterat ett altname till enp2s0, vilket vi funderar på vad det är (förmodligen proxmox)

Vi tar hjälp av Nätverks killarna (Robert, Marcus, Martin, )
- Testat olika kablar, funkar inte
-  Testat lite olika syntax I konfigurationsfilen, gör ingen skillnad
- testat ethernet uttagen
- testat köra utan proxmox virtuella brygga
- testat ta bort enxf4 interfacet som är altname till ethernet
- switch gateway säkerhet
- tog hjälp av AI, altname är ett sätt att förutse predictable mac adresser (?)
- Porten skickar data, skickar stp meddelande men ingen arp
- Robert testar packet capture på switchen
- Den skickar in men fåt inge svar, den arpar

- ARP Address Resolution Protocol, translates mac addresses to IP-addresses, every connected device sends an ARP request to the switch and recieves an ARP reply. On the client and server you have arp tables, keeping track of which mac address belongs to which ip address.

- Martin kom på att det kan ha att göra med att vårat VLAN inte är konfigurerat på port channel



Github: 
@robertbrokull

-->


<br><br>

## Target Audience
This repo is for anyone who wants a step-by-step guide on installing Proxmox VE.
This repo is also part of a larger project aimed at people interested in learning about IaC, and building such an environment from scratch. 
<br>

## Document Status
> [!NOTE]  
> This is a work in progress.<br>
> This repo is part of a larger ongoing project.
<br>

## Disclaimer
> [!CAUTION]
> This is intended for learning, testing, and experimentation. The emphasis is not on security or creating an operational environment suitable for production.

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
We would like to thank <a href=https://github.com/rafaelurrutiasilva>Rafael Urrutia</a> for his continuous support and guidance and the ITI team at SMHI Norrköping. 
<br>

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
