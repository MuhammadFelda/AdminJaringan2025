# 📧 Protokol dan Sistem Mail Server

## 1. Protokol Mail

Protokol mail adalah aturan komunikasi yang digunakan untuk mengirim, menerima, dan mengelola email. Protokol utama yang digunakan meliputi:

### 🔹 SMTP (Simple Mail Transfer Protocol)
- **Fungsi**: Mengirim email dari klien ke server, dan dari satu server ke server lain.
- **Port default**:
  - 25 (umum, tidak terenkripsi)
  - 587 (dengan STARTTLS - terenkripsi)
  - 465 (dengan SSL/TLS - deprecated tapi masih digunakan)
- **Sifat**: Hanya untuk mengirim email, bukan menerima.
- **Contoh**: Ketika kamu menekan tombol “Kirim” pada email, SMTP digunakan untuk menyampaikan email ke server tujuan.

### 🔹 POP3 (Post Office Protocol v3)
- **Fungsi**: Mengambil email dari server ke klien.
- **Port default**:
  - 110 (tanpa enkripsi)
  - 995 (dengan SSL/TLS – dikenal sebagai POP3S)
- **Sifat**:
  - Email diunduh dan dihapus dari server secara default (bisa diubah).
  - Cocok untuk pengguna yang hanya menggunakan satu perangkat.
- **Kelemahan**: Email tidak dapat diakses dari perangkat lain karena sudah diunduh ke perangkat pertama.

### 🔹 IMAP (Internet Message Access Protocol)
- **Fungsi**: Mengakses email langsung dari server, tanpa mengunduh secara permanen.
- **Port default**:
  - 143 (tanpa enkripsi)
  - 993 (dengan SSL/TLS)
- **Sifat**:
  - Sinkronisasi dua arah antara server dan klien.
  - Email tetap di server, cocok untuk akses dari banyak perangkat.
- **Kelebihan**: Memungkinkan pengelolaan folder dan pencarian email langsung di server.

### 🔹 POP3S (POP3 Secure)
- **Fungsi**: Sama seperti POP3, tetapi menggunakan SSL/TLS untuk enkripsi.
- **Port**: 995
- **Tujuan**: Menjaga keamanan transmisi email saat mengambil dari server.

---

## 2. Informasi Mail Server dalam Sebuah Domain

Agar domain dapat mengirim dan menerima email, DNS harus dikonfigurasi dengan record yang tepat.

### 🔹 DNS Record Terkait Mail Server

#### MX (Mail Exchanger) Record
- Menentukan mail server yang bertanggung jawab untuk menerima email atas nama domain.
- Bisa memiliki prioritas (nilai lebih rendah = prioritas lebih tinggi).
- Contoh:
  ```
  example.com.   IN   MX   10 mail.example.com.
  ```

#### A Record / AAAA Record
- Digunakan untuk menunjuk alamat IP (IPv4/IPv6) dari hostname MX.
- Contoh:
  ```
  mail.example.com. IN A 192.0.2.1
  ```

#### SPF (Sender Policy Framework)
- Record TXT yang mendefinisikan server mana yang diizinkan mengirim email.
- Membantu mencegah spoofing.
- Contoh:
  ```
  example.com. IN TXT "v=spf1 ip4:192.0.2.1 include:_spf.google.com ~all"
  ```

#### DKIM (DomainKeys Identified Mail)
- Menggunakan kunci publik dalam DNS untuk memverifikasi keaslian email.
- Record TXT dengan nama seperti: `default._domainkey.example.com`.

#### DMARC (Domain-based Message Authentication, Reporting and Conformance)
- Menentukan kebijakan autentikasi email dan bagaimana menangani email yang gagal diverifikasi.

### 🔹 Cara Melihat Informasi Mail Server
- **Linux Terminal**:
  ```bash
  dig MX example.com
  ```
- **Web Tools**:
  - [https://mxtoolbox.com](https://mxtoolbox.com)
  - [https://dnschecker.org](https://dnschecker.org)

---

## 3. Introduction to Electronic Mail

### 🖼️ Penjelasan Gambar Sistem Email

Gambar tersebut menggambarkan alur pengiriman dan penerimaan email antara dua pengguna: User A dan User B.

### 🔧 Komponen Utama dalam Sistem Email

| Komponen       | Keterangan |
|----------------|------------|
| UA (User Agent)| Aplikasi yang digunakan pengguna untuk menulis, membaca, dan mengelola email. Contoh: Outlook, Thunderbird, Webmail. |
| Spool          | Buffer atau antrian lokal tempat email disimpan sementara sebelum dikirim oleh MTA. |
| Mailboxes      | Tempat penyimpanan permanen untuk email yang diterima. |
| Alias Expander | Komponen yang memetakan alias email ke alamat sebenarnya. |
| Database       | Menyimpan informasi konfigurasi, alias, dan informasi tambahan lainnya. |
| MTA            | Mail Transfer Agent, bertanggung jawab mengirim dan menerima email antar server. Contoh: Postfix, Sendmail, Exim. |
| Client/Server  | Menunjukkan peran MTA, bisa sebagai klien (pengirim) atau server (penerima). |
| Internet       | Jalur komunikasi antara MTA pengirim dan penerima. |

### 🔄 Alur Pengiriman Email dari User A ke User B
1. User A menulis email melalui UA.
2. Email disimpan sementara di Spool.
3. Alias Expander memeriksa apakah alamat tujuan adalah alias.
4. MTA client mengambil email dari spool.
5. MTA mengirimkan email ke MTA server domain tujuan melalui Internet.
6. MTA server User B menerima dan menyimpan ke mailbox.
7. User B menggunakan UA untuk membaca email.

### 🧭 Fungsi Antar Komponen
- **UA ↔ Mailbox**: Untuk membaca email yang diterima.
- **UA → Spool**: Menyimpan email yang akan dikirim.
- **Alias Expander ↔ Database**: Mengecek informasi alias.
- **MTA (Client) → MTA (Server)**: Mengirim email antar domain.