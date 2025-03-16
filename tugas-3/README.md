# Workshop Administrasi Jaringan: NTP dan Samba

## Dibuat oleh
**Muhammad Felda Hibatullah**  
**NIM: 3213600023**  
**Kelas: 2 D4 Teknik Informatika A**

---

# Instalasi NTP Client

NTP (Network Time Protocol) digunakan untuk sinkronisasi waktu dengan server NTP. Berikut adalah langkah-langkah instalasi dan konfigurasi NTP Client agar host memiliki waktu yang sinkron dengan NTP server di Indonesia.

### **Referensi**
- [Server World - Debian 12 NTP](https://www.server-world.info/en/note?os=Debian_12&p=ntp&f=1)
- [NTP Pool Server Indonesia](https://www.ntppool.org/en/zone/id)

### **Package terkait**
- `ntp`
- `ntpsec`

### **Langkah-langkah**

1. **Instalasi NTP**
   ```bash
   sudo apt install ntp
   ```

2. **Konfigurasi pool server NTP**
   Edit file konfigurasi `/etc/ntpsec/ntp.conf`, ubah default pool server ke pool server Indonesia:
   ```bash
   sudo nano /etc/ntpsec/ntp.conf
   ```
   Ganti baris pool default dengan:
   ```
   pool id.pool.ntp.org iburst
   ```

3. **Restart layanan NTP**
   ```bash
   sudo systemctl restart ntpd
   ```

4. **Verifikasi pool server**
   ```bash
   ntpq -p
   ```

---

# Instalasi dan Konfigurasi Samba

Samba digunakan untuk berbagi folder secara publik atau terbatas di jaringan. Berikut langkah-langkah untuk membuat public shared folder dan limited shared folder.

### **Referensi**
- [Server World - Debian 12 Samba](https://www.server-world.info/en/note?os=Debian_12&p=samba&f=1)

### **Package terkait**
- `samba`
- `smbclient`
- `cifs-tools`

### **Langkah-langkah**

1. **Instalasi Samba**
   ```bash
   sudo apt install samba
   ```

2. **Membuat folder untuk sharing**
   ```bash
   mkdir /home/share
   sudo chmod 777 /home/share
   ```

3. **Konfigurasi Fully Access Shared Folder**
   Edit file konfigurasi Samba:
   ```bash
   sudo nano /etc/samba/smb.conf
   ```
   Tambahkan:
   ```
   [Public]
   path = /home/share
   browseable = yes
   writable = yes
   guest ok = yes
   ```

4. **Restart layanan Samba**
   ```bash
   sudo systemctl restart smbd
   ```

5. **Akses folder dari Windows (Map Network Drive)**
   - Gunakan `\IP_ADDRESS\Public` pada Windows Explorer.

---

# Rangkuman Package Management pada Debian 12

## 1. Instalasi dan Konfigurasi Awal

```bash
fdisk -l
lsblk
mkfs.ext4 /dev/sdX
mount /dev/sdX /mnt
timedatectl set-timezone Asia/Jakarta
hostnamectl set-hostname nama_host
adduser nama_pengguna
usermod -aG sudo nama_pengguna
```

## 2. Manajemen Paket dan Pembaruan Sistem

```bash
apt update
apt upgrade -y
apt install nama_paket
apt remove nama_paket
apt autoremove -y
apt-cache search nama_paket
```

## 3. Manajemen Pengguna dan Hak Akses

```bash
usermod -aG nama_grup nama_pengguna
groups nama_pengguna
chown pengguna:grup nama_file
chmod 755 nama_file
passwd nama_pengguna
```

## 4. Manajemen Proses dan Layanan

```bash
ps aux
top
kill PID
systemctl start nama_layanan
systemctl stop nama_layanan
systemctl status nama_layanan
systemctl enable nama_layanan
systemctl disable nama_layanan
```

## 5. Konfigurasi Jaringan

```bash
ip a
ip route
ip addr add 192.168.1.100/24 dev eth0
ping 8.8.8.8
```

## 6. Firewall dan Keamanan

```bash
ufw enable
ufw allow 22/tcp
ufw deny 80/tcp
ufw status verbose
```

## 7. Monitoring Sistem

```bash
free -h
tail -f /var/log/syslog
systemctl --failed
```

## 8. Automasi dengan Cron dan Scripting

```bash
crontab -e
chmod +x script.sh
./script.sh
```

## 9. Troubleshooting dan Recovery

```bash
fsck -y /dev/sdX
dpkg --configure -a
apt --fix-broken install
mv file.conf.bak file.conf
```