Fibocom Modem Wiki - FM350-GL / Dell DW5930e
=================================================
> :book: Living Wiki page for Fibocom FM350-GL 5G Sub-6 modems and Dell DW5930e / HP lt4320.

# Fibocom FM350-GL / Dell DW5930e / HP lt4320

![](../images/fm350_gl.png)

The **Fibocom FM350-GL** is a 5G Sub-6 NR and LTE Cat-19 M.2 3042/3052 module powered by the **MediaTek T700 5G** platform. Rebranded as the **Dell DW5930e** and **HP lt4320**, it delivers ultra-fast 5G connectivity for modern enterprise laptops and edge routing hardware.

---

# Table of Contents
- [Tech Specs](#tech-specs)
- [OEM Cross-Reference Table](#oem-cross-reference-table)
- [PCIe & Host Interface Details](#pcie--host-interface-details)
- [Linux & ModemManager Support](#linux--modemmanager-support)
- [FCC Unlock Procedure](#fcc-unlock-procedure)
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
| **Supported 5G Bands** | n1, n2, n3, n5, n7, n8, n12, n20, n25, n28, n38, n40, n41, n48, n66, n77, n78, n79 |
| **Host Interface** | PCIe Gen3 / USB 3.0 / USB 2.0 |
| **Primary Protocol** | MBIM over PCIe (`mtk_t700` driver) / MBIM over USB |
| **Operating Voltage** | 3.135V ~ 4.4V (Typ 3.3V) |

---

# OEM Cross-Reference Table

| Model / Branding | Manufacturer / OEM | USB / PCIe ID | Primary Protocol | Notes |
| :--- | :--- | :--- | :--- | :--- |
| **Fibocom FM350-GL** | Generic Fibocom | `0e8d:7127` / `0e8d:7126` | MBIM over PCIe | Retail & Developer boards |
| **Dell DW5930e** | Dell | `413c:81e0` | MBIM over PCIe | Dell Latitude 5000/7000, XPS |
| **HP lt4320** | HP | `03f0:0a84` | MBIM over PCIe | HP EliteBook 800/1000 G9+ |

---

# PCIe & Host Interface Details

The FM350-GL defaults to **PCIe Gen3** host interface on newer laptops instead of traditional USB host modes.

### Linux Driver Support:
- **Kernel 5.14+**: Includes native MediaTek T700 driver `mtk_t700` for PCIe initialization.
- **MBIM Interface**: Exposes `/dev/wwan0mbim0` network control device and `wwan0` interface via the Linux WWAN subsystem (`wwan.ko`).

---

# Linux & ModemManager Support

Ensure Modern **ModemManager 1.18+** is installed:

```bash
# Check modem status via mmcli
mmcli -L
mmcli -m 0
```

### Enable WWAN Driver:
```bash
modprobe wwan
modprobe mtk_t700
```

---

# FCC Unlock Procedure

Like other OEM cellular modules, the Dell DW5930e and HP lt4320 require an FCC lock challenge/response unlock sequence.

ModemManager handles this automatically when `libmbim` and ModemManager's FCC scripts are configured:

```bash
# Force FCC unlock challenge via AT port if required
AT+CFUN=1
```

---

# AT Commands

| Function | Command |
| :--- | :--- |
| **Check Firmware & Revision** | `ATI` |
| **Check SIM Status** | `AT+CPIN?` |
| **Check 5G/4G Signal Info** | `AT+CESQ` |
| **Network Operator Registration** | `AT+COPS?` |
| **Check 5G Network Mode** | `AT+E5GSTAT?` |
| **Set Radio On** | `AT+CFUN=1` |

---

# Related Pages
- [Fibocom L850-GL / Dell DW5820e](../l850-gl/L850-GL.md)
- [Fibocom L860-GL / Dell DW5821e](../l860-gl/L860-GL.md)
- [Fibocom Modem Wiki Home](../README.md)
