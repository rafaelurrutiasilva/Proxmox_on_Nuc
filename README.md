# Installing Proxmox on an Asus PN64
<img width="400" alt="" src="https://github.com/rafaelurrutiasilva/Proxmox_on_Nuc/blob/main/Images/Proxmox_on_nuc.png?raw=true" align=left><br>
**Installing Proxmox on an Asus PN64**
<br>**Authors:** _Filip Andersson, Jonatan Högild_<br>
Publiceringsdatum<br>
<br>

## Abstract
First internship project, Installation of Proxmox on an Asus PN64 ax210NGW (NUC/Mini-PC). <br>
<br><br>
## Introduction<br>

### Greetings!
_...and welcome to our project. This project is a part of a series of projects with the end goal of setting up a complete virtualized, automated, and monitored IT-Enviroment as a part of our internship on SMHI's IT-department. The second goal of these projects are also supposed to serve as a set-up guide here on Github for anyone and everyone that wants to follow along! we will link every project to each other aswell._<br>

<br>**/Filip Andersson, Jonatan Högild**<br>
<!-- Inledning - Bakgrund och syfte. Eventuell översiktbild här -->
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
11. [Conclusion](#conclusion)


<br>

## Goals and Objectives
This is part of a larger ongoing IaC-project (Infrastructure as Code) that will use Proxmox as a base. 
The goal of this project is to build a complete IT-environment and gain a deeper understanding of the underlying components and their part in a larger production chain. 


## Method
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
   - Network settings can be added now, or later.
  
6. Once installation is complete, configure network settings:
   - Open /etc/network/interfaces

## Target Audience
_Målgrupp_

## Document Status
> [!NOTE]  
> This is a work in progress.
> This repo is part of a larger ongoing project.


## Disclaimer
> [!CAUTION]
> This is intended for learning, testing, and experimentation. The emphasis is not on security or creating an operational environment suitable for production.

## Scope and Limitations
Scope
* Provide step-by-step instructions for installing Proxmox VE on consumer or enterprise PC hardware.
* Cover basic system preparation (BIOS settings, virtualization options, storage configuration, network setup).
* Include post-installation essentials such as initial configuration, web UI access, storage setup, and basic VM/LXC creation.

Limitations
* This guide is not intended for production-grade, multi-node clusters or advanced HA setups.
* Hardware compatibility varies; the repository cannot guarantee support for all motherboards, NICs, or RAID controllers.
* Does not cover enterprise features such as Ceph clusters, advanced storage backends, or commercial subscription setups.
* Network configurations are limited to basic setups (e.g., single NIC, simple bridges) and may not apply to complex environments.
* Security hardening is only covered at a basic level—users are responsible for implementing their own security policies.
* Instructions may become outdated as Proxmox VE updates; always verify with the official documentation.

## Environment
Windows 10 was used for downloading and burning the Proxmox .ISO file. 
Proxmox uses a Debian base with a CLI. 

## Acknowledgments
We would like to thank <a href=https://github.com/rafaelurrutiasilva>Rafael Urrutia</a> for his continuous support and guidance and the ITi team at SMHI. 

## References
_Referenser (om det behövs)_

## Conclusion
Slutsats
