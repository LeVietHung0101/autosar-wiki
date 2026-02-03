---
title: Home
layout: home
nav_order: 1
permalink: /
---

Đây là tài liệu học tập AUTOSAR cá nhân.

---

<!-- 

&#x2BA9;  ⮩

<table class="hover-table">
  <thead>
    <tr>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>

 -->

<!-- 
AUTOSAR Roadmap

Autosar
    https://www.mhtechin.com/support/autosar-roadmap/
    https://www.cselectricalandelectronics.com/complete-autosar-roadmap-with-free-learning-resources/

Platform
    https://www.autosar.org/standards/classic-platform
    https://www.autosar.org/standards/adaptive-platform
    https://www.autosar.org/search?tx_solr%5Bfilter%5D%5B0%5D=category%3AR25-11&tx_solr%5Bfilter%5D%5B1%5D=platform%3ACP&tx_solr%5Bq%5D=
    https://www.laptrinhdientu.com/2023/01/Autosar01.html?m=1#thiet-bi-ngoai-khong-ho-tro

Security access
    https://fpt.auto/autosar/autosar-safety-and-security/
    https://piembsystech.com/security-access-service-identifier-0x27-uds-protocol/?amp=1
    https://youtu.be/Zv4Aag_vmrE?si=Ox2Y7AAV3GtUPwhg
    https://www.ijcaonline.org/archives/volume175/number26/holla-2020-ijca-920811.pdf

Uds
    https://www.dgtech.com/wp-content/uploads/2019/08/WP1903_UDS_V01.pdf
    https://www.google.com/url?sa=t&source=web&rct=j&opi=89978449&url=https://raw.githubusercontent.com/Microrain-zh/uds_protocol/master/ISO_14229-1_2013.en.PDF.pdf&ved=2ahUKEwjB65OHw6-SAxUVZ_UHHUX5CkMQFnoECFkQAQ&usg=AOvVaw3c3MPrPedyMbECGlE9NBoi
    https://arxiv.org/pdf/2206.12653

Fbl
    https://share.google/e3MnBXpgPXW8mBjsj
    https://www.vector.com/int/en/products/products-a-z/embedded-software/flash-bootloader/#
    https://cdn.vector.com/cms/content/products/Flash_Bootloader/Docs/FlashBootloader_ProductCatalogue_EN.pdf
    https://www.embitel.com/blog/embedded-blog/what-is-flash-bootloader-and-nuances-of-an-automotive-ecu-re-programming
    https://www.autosar.org/fileadmin/standards/R22-11/CP/AUTOSAR_SWS_FlashDriver.pdf




AUTOSAR Roadmap
    Ref: https://www.cselectricalandelectronics.com/complete-autosar-roadmap-with-free-learning-resources/



AUTOSAR Classic Platform
    Architecture Overview





├── AUTOSAR Classic Platform  
│   ├── Architecture Overview  
│   │   ├── Application Layer  
│   │   │   ├── Software Components (SWC)  
│   │   │   ├── Runnable entities  
│   │   │   └── Ports (Sender/Receiver, Client/Server)  
│   │   ├── Runtime Environment (RTE)  
│   │   │   ├── Middleware  
│   │   │   ├── Connects SWCs to BSW  
│   │   │   └── Auto-generated code  
│   │   └── Basic Software (BSW)  
│   │       ├── OS  
│   │       ├── Communication stack  
│   │       ├── Memory services  
│   │       └── ECU abstraction  
│   ├── Application Layer (SWC)  
│   │   ├── Key Concepts  
│   │   │   ├── Software Components (Atomic, Composite)  
│   │   │   ├── Ports and Interfaces  
│   │   │   ├── Runnables  
│   │   │   └── Events  
│   │   ├── Learn  
│   │   │   ├── Sender-Receiver Interface  
│   │   │   ├── Client-Server Interface  
│   │   │   └── Mode Switch Interface  
│   │   └── Resources  
│   │       ├── YouTube - AUTOSAR Explained – Learn Automotive  
│   │       ├── YouTube - FastBit AUTOSAR Classic Free Series  
│   │       └── AUTOSAR official docs (autosar.org – free PDFs)  
│   ├── Runtime Environment (RTE)  
│   │   ├── What RTE Does  
│   │   │   ├── Generates glue code  
│   │   │   ├── Ensures communication between SWCs  
│   │   │   └── Isolates application from hardware  
│   │   ├── Learn  
│   │   │   ├── RTE APIs  
│   │   │   ├── Runnable triggering  
│   │   │   └── Data access methods  
│   │   └── Resources  
│   │       ├── YouTube - RTE Explained – FastBit  
│   │       └── AUTOSAR Classic Specification (free reading)  
│   ├── AUTOSAR OS  
│   │   ├── Based on OSEK/VDX  
│   │   ├── Learn  
│   │   │   ├── Tasks (Basic / Extended)  
│   │   │   ├── ISRs (Category 1 & 2)  
│   │   │   ├── Events  
│   │   │   ├── Alarms  
│   │   │   ├── Resources  
│   │   │   └── Scheduling  
│   │   └── Resources  
│   │       ├── YouTube - OSEK OS Explained  
│   │       ├── FreeRTOS vs AUTOSAR OS comparison videos  
│   │       └── AUTOSAR OS specification PDFs  
│   ├── Basic Software (BSW)  
│   │   ├── ECU Abstraction Layer  
│   │   │   ├── ADC  
│   │   │   ├── PWM  
│   │   │   ├── Dio  
│   │   │   └── Port  
│   │   ├── MCAL (Microcontroller Abstraction Layer)  
│   │   │   └── Direct hardware access (Vendor-specific)  
│   │   ├── Communication Stack  
│   │   │   ├── CANIF  
│   │   │   ├── CANTP  
│   │   │   ├── COM  
│   │   │   └── PDU Router  
│   │   ├── Memory Stack  
│   │   │   ├── NvM  
│   │   │   ├── Fee  
│   │   │   └── Ea  
│   │   └── Resources  
│   │       ├── YouTube - BSW Explained – FastBit  
│   │       ├── Blogs - MicrocontrollerTips  
│   │       └── AUTOSAR spec PDFs  
│   ├── Diagnostics  
│   │   ├── Uses UDS (ISO 14229)  
│   │   ├── Learn  
│   │   │   ├── DCM  
│   │   │   ├── DEM  
│   │   │   └── Diagnostic Services (0x10, 0x11, 0x22, 0x31)  
│   │   └── Resources  
│   │       ├── YouTube - UDS Explained  
│   │       ├── Blogs - CANoe Diagnostics Tutorials  
│   │       └── ISO UDS overview articles (free)  
│   └── AUTOSAR Tools  
│       ├── Open-Source Tools  
│       │   ├── Artop (AUTOSAR Tool Platform)  
│       │   ├── Eclipse AUTOSAR plugins  
│       │   └── EB Tresos trial (limited)  
│       ├── Simulation  
│       │   ├── Wokwi (for logic understanding)  
│       │   └── CANoe basics (free videos)  
│       └── Resources  
│           ├── Eclipse AUTOSAR GitHub  
│           ├── Artop Documentation  
│           └── YouTube Tool Demo Videos  





UDS
    SIDs
        Diagnostic Session Control (0x10)
        ECU Reset (0x11)
        Security Access (0x27)
        ...
    NRC
    DTC


















-->
