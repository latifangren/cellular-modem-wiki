Quectel Modem Wiki - EM12-G / EM120R-GL
===========================================
> :book: Living Wiki page for Quectel EM12-G & EM120R-GL LTE Cat-12 M.2 modems.

# Quectel EM12-G / EM120R-GL

![](../images/em12g.png)

The **Quectel EM12-G** (and updated **EM120R-GL**) is a global LTE Cat-12 M.2 3042 module powered by the **Qualcomm Snapdragon X12** (SDX12) modem platform. Capable of up to 600 Mbps downlink and 150 Mbps uplink, it is one of the most widely used 4G LTE modems in custom routers and open-source networking setups.

---

# Table of Contents
- [Tech Specs](#tech-specs)
- [USB VID:PID & Protocols](#usb-vidpid--protocols)
- [OpenWrt / Linux Driver Configuration](#openwrt--linux-driver-configuration)
- [AT Commands](#at-commands)
- [Related Pages](#related-pages)

---

# Tech Specs

| Specification | Value |
| :--- | :--- |
| **Chipset** | Qualcomm SDX12 (Snapdragon X12) |
| **Form Factor** | M.2 Key-B 3042 |
| **LTE Category** | LTE Cat-12 (600 Mbps DL / 150 Mbps UL) |
| **Carrier Aggregation** | 3CA Downlink, 2CA Uplink |
| **MIMO** | 2x2 MIMO |
| **Supported Bands (FDD)** | B1, B2, B3, B4, B5, B7, B8, B9, B12, B13, B14, B17, B18, B19, B20, B26, B28, B29, B30, B32, B66 |
| **Supported Bands (TDD)** | B38, B39, B40, B41 |
| **Protocols Supported** | QMI (`qmi_wwan`), MBIM (`cdc_mbim`), ECM, RNDIS, Serial (`qcserial`) |

---

# USB VID:PID & Protocols

| Model | USB VID:PID | Default Driver | Notes |
| :--- | :--- | :--- | :--- |
| **EM12-G** | `2c7c:0125` | `qmi_wwan` / `cdc_mbim` | Standard retail version |
| **EM120R-GL** | `2c7c:0800` | `qmi_wwan` / `cdc_mbim` | Updated release |

---

# OpenWrt / Linux Driver Configuration

### Switch Protocol Mode via AT Command:

```text
# QMI Mode (default):
AT+QCFG="usbnet",0

# ECM Mode (Ethernet emulation):
AT+QCFG="usbnet",1

# MBIM Mode:
AT+QCFG="usbnet",2
```

Restart modem after setting mode:
```text
AT+CFUN=1,1
```

---

# AT Commands

| Function | Command |
| :--- | :--- |
| **Check Firmware Version** | `ATI` |
| **Check Signal & RSRP** | `AT+CSQ` / `AT+QRSRP` |
| **Band Lock Query** | `AT+QCFG="band"` |
| **Network Provider Info** | `AT+COPS?` |

---

# Related Pages
- [Quectel EM160R-GL Cat-16](./EM160R-GL.md)
- [Quectel EP06 / EM06 Cat-6](./EP06-EM06.md)
- [Quectel Modem Wiki Home](../README.md)
