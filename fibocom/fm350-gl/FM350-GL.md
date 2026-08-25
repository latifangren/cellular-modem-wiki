Fibocom Modem Wiki - FM350-GL / HP lt4320
=========================================
> :book: Living Wiki page for Fibocom FM350-GL 5G Sub-6 modems and HP lt4320.

# Fibocom FM350-GL / HP lt4320

![](../images/fm350_gl.png)

The **Fibocom FM350-GL** is a 5G Sub-6 NR and LTE Cat-19 M.2 3042/3052 module powered by the **MediaTek T700 5G** platform. Rebranded as **HP lt4320**, it delivers 5G connectivity for enterprise laptops and edge routing hardware.

---

# Table of Contents
- [Tech Specs](#tech-specs)
- [OEM VID:PID Reference](#oem-vidpid-reference)
- [PCIe & Host Interface Details](#pcie--host-interface-details)
- [Linux & ModemManager Support](#linux--modemmanager-support)
- [AT Commands](#at-commands)
- [Related Pages](#related-pages)

---

# Tech Specs

| Specification | Value |
| :--- | :--- |
| **Chipset** | MediaTek T700 (5G Modem SoC) |
| **Form Factor** | M.2 Key-B 3042 / 3052 |
| **5G Modes** | 5G NR SA / NSA (Sub-6 GHz) |
| **5G Data Rate** | Up to 4.7 Gbps Downlink / 1.25 Gbps Uplink |
| **LTE Category** | LTE Cat-19 |
| **MIMO** | 4x4 MIMO (5G Sub-6 & LTE High Bands) |
| **Host Interface** | PCIe Gen3 / USB 3.0 |
| **Primary Protocol** | MBIM over PCIe (`mtk_t700` driver) / MBIM over USB |

---

# OEM VID:PID Reference

| Model / Branding | Manufacturer / OEM | USB / PCIe ID | Primary Protocol |
| :--- | :--- | :--- | :--- |
| **Fibocom FM350-GL** | Generic Fibocom | `0e8d:7127` / `0e8d:7126` | MBIM over PCIe |
| **HP lt4320** | HP | `03f0:0a84` | MBIM over PCIe |

*Note: Dell DW5930e is NOT an FM350-GL; Dell DW5930e is a Qualcomm Snapdragon X55-based Foxconn T99W175 module.*

---

# PCIe & Host Interface Details

The FM350-GL communicates via **PCIe Gen3** on Linux using the `mtk_t700` kernel driver (Linux 5.14+).

---

# AT Commands

| Function | Command |
| :--- | :--- |
| **Check Firmware Revision** | `ATI` |
| **Check SIM Status** | `AT+CPIN?` |
| **Check 5G Signal** | `AT+CESQ` |

---

# Related Pages
- [Foxconn T99W175 / Dell DW5930e](../../foxconn/T99W175.md)
- [Fibocom L850-GL](../l850-gl/L850-GL.md)
- [Fibocom Modem Wiki Home](../README.md)
