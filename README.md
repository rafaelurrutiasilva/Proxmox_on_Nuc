# Title Page
<img width="100" alt="MyLogo" src="https://github.com/rafaelurrutiasilva/images/blob/main/logos/MyLogo_2.png" align=left>_Logon modifieras så att det passar projektet._<br>
<br>
Installing Proxmox on Asus PN64<br>
Filip Andersson, Jonatan Högild<br>
Publiceringsdatum<br>_
<br>

## Abstract
_Kort sammanfattning av dokumentet._
Installation of Proxmox on an Asus PN64 ax210NGW (NUC/Mini-PC). 

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

## Introduction
_Inledning - Bakgrund och syfte. Eventuell översiktbild här._


## Goals and Objectives
_Mål och syften._
This is part of a larger ongoing IaC-project that will use Proxmox as a base. 
The goal of this project is to build a complete IT-environment and gain a deeper understanding of the underlying components and their part in a larger production chain. 


## Method

1. Proxmox VE 9.1 was downloaded from the official site: https://proxmox.com/en/downloads/proxmox-virtual-environment/iso

2. A SHA256 checksum is provided for each .ISO. This hash can be confirmed on Windows using the powershell command Get-FileHash .\proxmox-ve_9.0.1.iso -Algorithm SHA 256

3. Burn the .ISO file to a USB-stick using Rufus (https://rufus.ie).

4. Plug in the USB to the Asus machine and go into the UEFI settings. Configure the following:
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
_Dokumentstatus (I enlighet med ISO 9001: Utkast → Granskad → Godkänd → Publicerad → Arkiverad)_
> [!NOTE]  
> My work here is not finished yet. I need, among other things, to supplement with instructions on how each component should be configured to work together as well supplement with an overview image that explains how the whole thing works.


## Disclaimer
Ansvarsfriskrivning. Tex:
> [!CAUTION]
> This is intended for learning, testing, and experimentation. The emphasis is not on security or creating an operational environment suitable for production.

## Scope and Limitations
_Omfattning och begränsningar_

## Environment
_Miljö som användes_

## Acknowledgments
_Tack och erkännanden. Tex:_
Big thanks to all the people involved in the material I refer to in my links! I would also like to express gratitude to everyone out there, including my colleagues and friends, who are creating things that help and inspire us to continue learning and exploring this never-ending world of computer technology.

## References
_Referenser (om det behövs)_

## Conclusion
Slutsats
