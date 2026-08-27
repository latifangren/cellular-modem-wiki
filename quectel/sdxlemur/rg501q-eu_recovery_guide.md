# Panduan Pemulihan Lengkap Quectel RG501Q-EU Pasca Reset Pabrik (Factory Reset)

Dokumentasi ini mencakup langkah-langkah pemulihan modem **Quectel RG501Q-EU (Qualcomm SDX55)** setelah dilakukan `ResetFactory`, dari pengaktifan port ADB, pemulihan Dropbear SSH, build & deployment **QManager-GO WebUI**, hingga pengaktifan dual-port LAN Realtek RTL8125.

---

## 📋 Ringkasan Perangkat & Lingkungan
- **Perangkat Modem:** Quectel RG501Q-EU (SDX55 5G Sub-6G Module)
- **Carrier Board:** Custom Carrier Board dengan 1 USB Port + 2 LAN Ports (Realtek RTL8125 2.5G)
- **Koneksi:** USB dicolok ke Linux Host / PC Assistant, Port LAN dicolok ke PC Windows (Static IP `192.168.225.10`)
- **OS Modem:** OpenWrt / Embedded Linux (Kernel 4.14.206)

---

## 1. Mengaktifkan Kembali ADB (Android Debug Bridge)

Setelah `ResetFactory`, service `adbd` dan port USB composition ter-reset sehingga modem tidak terdeteksi oleh `adb devices`.

### Langkah-langkah Pemulihan ADB:
1. Hubungkan ke AT Port modem (`/dev/ttyUSB2` atau `/dev/smd11`).
2. Dapatkan **ADB Key Challenge** dari modem:
   ```text
   AT+QADBKEY?
   ```
   *Respon modem:* `+QADBKEY: 40079162`

3. Generate **QADBKEY Unlock Hash** (menggunakan algoritma MD5 Salt Quectel 5G):
   - Crypt Salt: `$1$<QADBKEY>$`
   - Secret Passphrase: `SH_adb_quectel`
   - Ambil karakter indeks ke-12 sampai ke-27 sebagai unlock key.
   - *Hasil Key:* `uw9UiFVcMVpeARv`

4. Kirim Unlock Key dan Ubah USB Composition:
   ```text
   AT+QADBKEY="uw9UiFVcMVpeARv"
   AT+QCFG="usbcfg",0x2C7C,0x0801,1,1,1,1,1,1,0
   AT+CFUN=1,1
   ```

5. Verifikasi ADB:
   ```bash
   adb devices
   # Output: eee3f2ef    device
   ```

---

## 2. Restore Entware & Dropbear SSH Daemon

Akibat `ResetFactory`, direktori `/opt` pada modem menjadi kosong, sehingga service `dropbear.service` (SSH) gagal berjalan.

### Langkah-langkah Pemulihan SSH:
1. **Setup Direct Bind Mount & Directory Structure:**
   ```bash
   adb shell "mount -o remount,rw / && mkdir -p /usrdata/opt && mkdir -p /opt && mount --bind /usrdata/opt /opt"
   adb shell "mkdir -p /opt/bin /opt/sbin /opt/etc/dropbear /opt/lib"
   ```

2. **Deploy Binary Dropbear & Linker:**
   - Symlink dynamic linker: `ln -sf /lib/ld-linux-armhf.so.3 /opt/lib/ld-linux.so.3`
   - Push binary `dropbear`, `dbclient`, dan `dropbearkey` ke `/opt/sbin/` dan `/opt/bin/`.
   - Generate hostkey:
     ```bash
     adb shell "/opt/bin/dropbearkey -t rsa -f /opt/etc/dropbear/dropbear_rsa_host_key"
     adb shell "/opt/bin/dropbearkey -t ed25519 -f /opt/etc/dropbear/dropbear_ed25519_host_key"
     ```

3. **Reset Password Root & Systemd Service:**
   - Kosongkan password root di `/usrdata/etc/shadow`:
     `root::19506:0:99999:7:::`
   - Buat unit service `systemd` `/lib/systemd/system/dropbear.service`:
     ```ini
     [Unit]
     Description=Dropbear SSH Server
     After=network.target

     [Service]
     Type=simple
     ExecStart=/opt/sbin/dropbear -F -E -R -B -p 22
     Restart=always

     [Install]
     WantedBy=multi-user.target
     ```
   - Enable & start service:
     ```bash
     adb shell "systemctl daemon-reload && systemctl enable dropbear && systemctl start dropbear"
     ```

---

## 3. Build & Deployment QManager-GO WebUI

QManager-GO merupakan WebUI modern berbasis **Go Single Binary** dengan embedded static frontend Next.js 16 + React 19.

### Langkah Build di Host PC:
1. **Clone Repository:**
   ```bash
   git clone https://github.com/evairo176/QManager-GO.git
   cd QManager-GO
   ```
2. **Build Static Next.js Frontend:**
   ```bash
   npm install --legacy-peer-deps
   npm run build
   # Hasil export berada di folder out/
   ```
3. **Embed Frontend ke Backend Go & Cross-Compile untuk ARMv7:**
   ```bash
   rm -rf backend/web/out
   cp -r out backend/web/
   cd backend
   export CGO_ENABLED=0 GOOS=linux GOARCH=arm GOARM=7
   go build -ldflags="-s -w" -o dist/qmanager-core-armv7 ./cmd/server
   ```

### Langkah Deployment ke Modem:
1. Push binary ke modem:
   ```bash
   adb push dist/qmanager-core-armv7 /usrdata/qmanager-core
   adb shell "chmod +x /usrdata/qmanager-core"
   ```
2. Buat service systemd `/lib/systemd/system/qmanager-core.service`:
   ```ini
   [Unit]
   Description=QManager Go Core Service
   After=basic.target

   [Service]
   Type=simple
   ExecStart=/usrdata/qmanager-core
   Restart=always
   RestartSec=2
   KillMode=process
   Environment=PORT=80
   Environment=AT_DEVICE=/dev/ttyUSB2

   [Install]
   WantedBy=multi-user.target
   ```
3. Enable & start service QManager:
   ```bash
   adb shell "systemctl daemon-reload && systemctl enable qmanager-core && systemctl start qmanager-core"
   ```

---

## 4. Pengaktifan Dual-Port LAN Physical (Realtek RTL8125)

Secara default pasca reset, interface PCIe LAN chip (Realtek RTL8125) dalam kondisi `disabled` / `mode=0` sehingga port LAN tidak terhubung ke interface `bridge0` (IP `192.168.225.1`).

### Langkah-langkah Pengaktifan Port LAN:
1. **Aktifkan PCIe Controller & Driver RTL8125 via AT Command:**
   Kirim perintah berikut via AT port (`/dev/smd11` atau `/dev/ttyUSB2`):
   ```text
   AT+QCFG="pcie/mode",1
   AT+QETH="eth_driver","r8125",1
   AT+CFUN=1,1
   ```

2. **Verifikasi Interface Network:**
   Setelah modem reboot, interface `eth0` (Realtek RTL8125) akan muncul dengan status `UP`.

3. **Bridge `eth0` ke `bridge0`:**
   Masukan interface fisik `eth0` ke bridge LAN modem:
   ```bash
   adb shell "brctl addif bridge0 eth0"
   ```

4. **Buat Autostart Bridge Service Persistent:**
   Buat file `/lib/systemd/system/bridge-eth0.service`:
   ```ini
   [Unit]
   Description=Bridge eth0 to bridge0 for LAN ports
   After=basic.target

   [Service]
   Type=oneshot
   ExecStart=/bin/sh -c '/sbin/brctl addif bridge0 eth0 || true'
   RemainAfterExit=yes

   [Install]
   WantedBy=multi-user.target
   ```
   Enable service:
   ```bash
   adb shell "systemctl daemon-reload && systemctl enable bridge-eth0.service && systemctl start bridge-eth0.service"
   ```

---

## ✅ Akses Layanan Modem (Final State)

- **SSH (Root):** `ssh root@192.168.225.1` (Port 22, tanpa password)
- **WebUI QManager-GO:** `http://192.168.225.1` (Port 80)
- **Konektivitas LAN:** PC Windows terhubung via port LAN RJ45 dengan IP `192.168.225.10` / DHCP `192.168.225.x`.
