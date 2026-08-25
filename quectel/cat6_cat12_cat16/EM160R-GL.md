Quectel Modem Wiki - EM160R-GL
===============================
> :book: Living Wiki page for Quectel EM160R-GL Gigabit LTE Cat-16 M.2 modems.

# Quectel EM160R-GL

![](../images/em160.png)

The **Quectel EM160R-GL** is an advanced Gigabit LTE Cat-16 M.2 3042 module based on the **Qualcomm Snapdragon X12** platform architecture. Delivering up to 1.0 Gbps downlink speeds with 4x4 MIMO and 5CA carrier aggregation support, it is a high-speed choice for enterprise 4G routers.

---

# Table of Contents
- [Tech Specs](#tech-specs)
- [USB VID:PID & Driver Support](#usb-vidpid--driver-support)
- [Mode Switching & OpenWrt Configuration](#mode-switching--openwrt-configuration)
- [AT Commands](#at-commands)
- [Related Pages](#related-pages)

---

# Tech Specs

| Specification | Value |
| :--- | :--- |
| **Chipset** | Qualcomm SDX12 (Cat-16 Variant) |
| **Form Factor** | M.2 Key-B 3042 |
| **LTE Category** | LTE Cat-16 (1 Gbps DL / 150 Mbps UL) |
| **Carrier Aggregation** | 5CA Downlink, 2CA Uplink |
| **MIMO** | 4x4 MIMO |
| **Supported Bands (FDD)** | B1, B2, B3, B4, B5, B7, B8, B12, B13, B14, B17, B18, B19, B20, B25, B26, B28, B29, B30, B32, B66 |
| **Supported Bands (TDD)** | B38, B39, B40, B41, B42, B43, B46 (LAA), B48 |
| **Host Interface** | USB 3.0 / USB 2.0 / PCIe |
| **Protocols Supported** | QMI (`qmi_wwan`), MBIM (`cdc_mbim`), PCIe (`qcom-qpx`) |

---

# USB VID:PID & Driver Support

| Model | USB VID:PID | Default Protocol |
| :--- | :--- | :--- |
| **EM160R-GL** | `2c7c:0620` | QMI (`qmi_wwan`) / MBIM (`cdc_mbim`) |

---

# Mode Switching & OpenWrt Configuration

```text
# Set to QMI Mode:
AT+QCFG="usbnet",0

# Set to MBIM Mode:
AT+QCFG="usbnet",2

# Reboot modem to apply:
AT+CFUN=1,1
```

---

# AT Commands

| Function | Command |
| :--- | :--- |
| **Check Firmware Revision** | `ATI` |
| **Check Signal & Carrier Aggregation** | `AT+QCAINFO` |
| **Check RSRP/RSRQ** | `AT+QRSRP` |
| **Radio Reset** | `AT+CFUN=1,1` |

---

# Related Pages
- [Quectel EM12-G Cat-12](./EM12-G.md)
- [Quectel Modem Wiki Home](../README.md)
