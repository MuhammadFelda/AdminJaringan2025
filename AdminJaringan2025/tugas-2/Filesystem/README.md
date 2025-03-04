# Bab 5: Filesystem

![filesystem-icon](https://miro.medium.com/v2/resize:fit:752/1*quw0WvsLLCxad3WC6fjQ1Q.png)

Tujuan dasar dari sistem berkas adalah untuk merepresentasikan dan mengatur sumber daya penyimpanan sistem.

Sistem berkas dapat dianggap sebagai sebuah sistem yang terdiri dari empat komponen utama:

1. Ruang nama - cara untuk menamai sesuatu dan mengaturnya dalam sebuah hirarki
2. API - sekumpulan panggilan sistem untuk menavigasi dan memanipulasi objek
3. Model keamanan - skema untuk melindungi, menyembunyikan, dan berbagi sesuatu
4. Implementasi - perangkat lunak untuk menghubungkan model logis ke perangkat keras

Sistem berkas berbasis disk yang utama adalah sistem berkas ext4, XFS, dan UFS, bersama dengan ZFS dan Btrfs dari Oracle. Masih banyak lagi yang lain yang tersedia, termasuk VxFS dari Verittas dan JFS dari IBM.

Selain itu, kami juga memiliki beberapa sistem berkas asing seperti FAT dan NTFS yang digunakan oleh Windows dan sistem berkas ISO 9660 yang digunakan oleh CD dan DVD.

Kebanyakan sistem berkas modern mencoba mengimplementasikan fungsionalitas sistem berkas tradisional dengan cara yang lebih cepat dan lebih dapat diandalkan, atau menambahkan fitur tambahan sebagai lapisan di atas semantik sistem berkas standar.

## Pathname

Kata "folder" hanyalah bocoran bahasa dari dunia Windows dan macOS. Artinya sama dengan **direktori**, yang lebih bersifat teknis. JANGAN menggunakannya dalam konteks teknis kecuali Anda siap menerima tatapan lucu!!!

Daftar direktori yang mengarah ke sebuah file disebut **pathname**. Nama path adalah sebuah string yang mendeskripsikan lokasi file di dalam hirarki sistem berkas.
Nama path dapat berupa **mutlak** (misal: `/home/username/file.txt`) atau **relatif** (misal: `./file.txt`).

## Filesystem Mounting and Unmounting
Sistem berkas terdiri dari potongan-potongan yang lebih kecil - disebut juga "sistem berkas" - yang masing-masing terdiri dari satu direktori dan subdirektori serta berkas-berkasnya. Kami menggunakan istilah **pohon berkas** untuk merujuk pada tata letak keseluruhan dan menggunakan kata **filesystem** untuk cabang-cabang yang melekat pada pohon tersebut.

Pada kebanyakan situasi, sistem berkas dilekatkan pada pohon dengan perintah `mount`. Perintah `mount` memetakan direktori di dalam pohon berkas yang ada, yang disebut **titik mount**, ke akar sistem berkas yang baru.

Contoh:

```bash
# Mount sistem berkas pada /dev/sda4 ke /users
mount /dev/sda4 /users
```

Linux memiliki opsi lazy unmount (**umount -l**) yang menghapus sistem berkas dari hirarki penamaan, namun tidak benar-benar meng-unmount hingga sistem berkas tersebut tidak lagi digunakan.

**umount -f** adalah unmount paksa, yang berguna ketika sistem berkas sedang sibuk.

Daripada menggunakan **umount -f**, Anda dapat menggunakan **lsof** atau **fuser** untuk mengetahui proses mana yang menggunakan sistem berkas dan kemudian mematikannya.

Contoh:

```bash
# Mengetahui proses mana yang menggunakan sistem berkas

abdou@debian:~$ lsof /home/abdou

PERINTAH PID PENGGUNA TIPE FD JENIS PERANGKAT UKURAN/OFF NAMA SIMPUL
bash 1000 abdou cwd DIR 8,1 4096 131073 /home/abdou
bash 1000 abdou rtd DIR 8,1 4096 131073 /home/abdou
bash 1000 abdou txt REG 8,1 103752 131072 /bin/bash
bash 1000 abdou mem REG 8,1 1848400 131074 /lib/x86_64-linux-gnu/libc-2.28.so
bash 1000 abdou mem REG 8,1 170864 131075 /lib/x86_64-linux-gnu/ld-2.28.so
code 1234 abdou cwd DIR 8,1 4096 131073 /home/abdou
msedge 5678 abdou cwd DIR 8,1 4096 131073 /home/abdou
```

Untuk menyelidiki proses yang menggunakan sistem berkas, Anda dapat menggunakan perintah **ps**.

Contoh:

```bash
abdou@debian:~$ ps up "1234 5678 91011"

USER PID %CPU %MEM VSZ RSS TTY STAT PERINTAH WAKTU MULAI
abdou 1234 0.0 0.0 12345 1234 ?        Ssl 00:00 0:00 kode
abdou 5678 0.0 0.0 12345 1234 ?        Ssl 00:00 0:00 msedge
abdou 91011 0.0 0.0 12345 1234 ?        Ssl 00:00 0:00 chrome
```

## Organization of teh File Tree
Sistem UNIX tidak pernah terorganisir dengan baik! Berbagai konvensi penamaan yang tidak kompatibel digunakan secara bersamaan, dan berbagai jenis file tersebar secara acak di sekitar ruang nama. **Karena itulah sulit untuk mengupgrade sistem operasi**.

Sistem berkas root mencakup setidaknya direktori root dan sekumpulan file dan subdirektori minimal. File yang berisi kernel OS biasanya berada di bawah **/boot**, tetapi nama dan lokasi yang tepat dapat bervariasi. Pada BSD dan beberapa sistem UNIX lainnya, kernel bukanlah sebuah file tunggal melainkan sekumpulan komponen.

**/etc** berisi file sistem dan konfigurasi yang penting. **/sbin** dan **/bin** untuk utilitas penting, dan terkadang **/tmp** untuk file sementara. Pada awalnya **/dev** merupakan bagian dari sistem berkas root, tetapi sekarang ini merupakan sistem berkas virtual yang di-mount secara terpisah.

Beberapa sistem menyimpan berkas-berkas pustaka bersama dan beberapa keanehan lainnya, seperti preprosesor C, di direktori **/lib** atau **/lib64**. Sistem lain telah memindahkan item-item ini ke dalam **/usr/lib**, terkadang meninggalkan **/lib** sebagai link simbolik.

Berkas **/usr** dan **/var** juga sangat penting.**/usr** adalah tempat penyimpanan program-program standar yang paling penting tetapi tidak penting bagi sistem, bersama dengan berbagai berkas lain seperti manual on-line dan sebagian besar pustaka.FreeBSD menyimpan cukup banyak konfigurasi locql di bawah **/usr/local**. **/var** menyimpan direktori spool, berkas log, informasi akuntansi, dan berbagai item lain yang berkembang atau berubah dengan cepat dan bervariasi pada setiap host.Baik **/usr** dan **/var** harus tersedia untuk memungkinkan sistem untuk masuk ke mode multiuser.

![pathnames](./data/pathnames.png)

## File Types

## Jenis-jenis file

Sebagian besar implementasi sistem berkas mendefinisikan tujuh jenis berkas:

1.File biasa
2. Direktori
3. File perangkat karakter
4. Memblokir file perangkat
5. Soket domain lokal
6. Pipa bernama (FIFO)
7. Tautan simbolik

Anda dapat menentukan jenis berkas dengan menggunakan perintah **file** (ketik **man file** untuk informasi lebih lanjut). 

```bash
$ file /bin/bash
bin/bash: ELF 64-bit LSB pie executable, x86-64, versi 1 (SYSV), tertaut secara dinamis, penerjemah /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=33a5554034feb2af38e8c75872058883b2988bc5, untuk GNU/Linux 3.2.0, stripped
```

Anda juga dapat menggunakan **ls -ld**, flag **-d** memaksa **ls** untuk menampilkan informasi direktori dan bukan menampilkan isi direktori.

![file-type-encoding](./data/file-type-encoding.png)

**Regular Files** terdiri dari serangkaian byte; sistem berkas tidak menerapkan struktur pada isinya.
File teks, file data, program yang dapat dieksekusi, dan pustaka bersama semuanya disimpan sebagai file biasa.

**Direktori** adalah nama referensi ke file lain. 

**Hard link** adalah cara untuk memberikan nama yang sama pada satu file. Perintah **ln** membuat hard link baru ke file yang sudah ada. Opsi **-i** pada **ls** akan menampilkan jumlah hard link ke setiap file.

Contoh :

```bash
$ ln /etc/passwd /tmp/passwd
```

**Character and block device files**
File perangkat memungkinkan program berkomunikasi dengan perangkat keras dan periferal sistem. Kernel menyertakan (atau memuat) perangkat lunak driver untuk setiap perangkat sistem. Perangkat lunak ini menangani detail yang berantakan dalam mengelola setiap perangkat sehingga kernel itu sendiri dapat tetap relatif abstrak dan tidak bergantung pada perangkat keras.

Perbedaan antara perangkat karakter dan blok tidak kentara dan tidak perlu ditinjau secara mendetail.

Berkas perangkat dicirikan oleh nomor perangkat mayor dan minor. Nomor mayor mengidentifikasi driver yang mengontrol perangkat, dan nomor minor biasanya memberi tahu driver unit fisik mana yang akan dituju. Sebagai contoh, nomor perangkat mayor 4 pada sistem Linux menunjukkan driver serial. Port serial pertama (**/dev/tty0**) akan memiliki nomor perangkat mayor 4 dan nomor perangkat minor 0, port serial kedua (**/dev/tty1**) akan memiliki nomor perangkat mayor 4 dan nomor perangkat minor 1, dan seterusnya.

Di masa lalu, **/dev** adalah direktori umum dan perangkat dibuat dengan **mknod** dan dihapus dengan **rm**. Sayangnya, sistem yang masih mentah ini tidak diperlengkapi untuk menangani banyaknya driver dan jenis perangkat yang muncul selama beberapa dekade terakhir. Sistem ini juga memfasilitasi semua jenis ketidakcocokan konfigurasi yang potensial: file perangkat yang tidak mengacu pada perangkat yang sebenarnya, perangkat yang tidak dapat diakses karena tidak memiliki file perangkat, dan sebagainya.

Sekarang ini, direktori **/dev** biasanya di-mount sebagai jenis sistem berkas khusus, dan isinya secara otomatis dipelihara oleh kernel bersama dengan daemon tingkat pengguna.

**Soket domain lokal adalah cara bagi proses untuk berkomunikasi satu sama lain. Soket ini mirip dengan soket jaringan, tetapi terbatas pada host lokal.

Syslog dan X Window System adalah contoh fasilitas standar yang menggunakan soket domain lokal.

**Named Pipes** seperti soket domain lokal memungkinkan proses yang sedang berjalan untuk berkomunikasi satu sama lain dalam host yang sama.

**Symbolic Links** atau soft link menunjuk ke file berdasarkan nama. Tautan ini merupakan cara untuk memberikan nama ganda pada berkas, tetapi lebih fleksibel daripada tautan keras. Tautan lunak dapat mengarah ke berkas pada sistem berkas yang berbeda, dan dapat mengarah ke direktori.

Sebagai contoh, direktori **/usr/bin** sering kali merupakan tautan simbolis ke **/bin**. Ini adalah cara untuk menjaga agar sistem berkas root tetap kecil dan memudahkan untuk berbagi perangkat lunak yang sama di antara beberapa hos.

```bash
$ ln -s /bin /usr/bin

$ ls -l /usr/bin
lrwxrwxrwx 1 root root 4 Mar  1  2020 /usr/bin -> /bin
```

## File attributes
Pada model sistem berkas Unix dan Linux, setiap berkas memiliki satu set sembilan bit hak akses, yang menentukan siapa saja yang dapat membaca, menulis, dan mengeksekusi berkas tersebut. Bersama dengan tiga bit lainnya yang terutama memengaruhi operasi program yang dapat dieksekusi, bit-bit ini merupakan mode file.

Kedua belas bit mode ini disimpan bersama dengan empat bit informasi tipe file. Empat bit tipe file ditetapkan ketika file dibuat dan tidak dapat diubah, tetapi pemilik file dan pengguna super dapat memodifikasi dua belas bit mode dengan perintah **chmod**.

![file-attributes](https://cdn.storyasset.link/nlFtWFR5rySdmletT0jhDUQ0tXl2/ms-yxhcfoletf.jpg)

### Permission bits
Bit-bit izin dibagi menjadi tiga kelompok yang masing-masing terdiri dari tiga bit. Kelompok pertama yang terdiri dari tiga bit adalah untuk pemilik berkas, kelompok kedua untuk kelompok berkas, dan kelompok ketiga untuk semua orang.

Anda dapat menggunakan nama H**ugo** untuk mengingat urutan kelompok: **u** untuk pemilik, **g** untuk grup, dan **o** untuk orang lain. 

Anda juga dapat menggunakan notasi oktal (basis 8) karena setiap digit dalam notasi oktal mewakili tiga bit. Tiga bit paling atas (dengan nilai oktal 400, 200, dan 100) mewakili pemilik berkas, tiga bit tengah (dengan nilai oktal 40, 20, dan 10) mewakili grup berkas, dan tiga bit paling bawah (dengan nilai oktal 4, 2, dan 1) mewakili semua orang.

Pada berkas biasa, bit read memungkinkan berkas untuk dibaca, bit write memungkinkan berkas untuk dimodifikasi atau dipotong; namun, kemampuan untuk menghapus atau mengganti nama (atau menghapus dan kemudian membuat ulang!) berkas dikendalikan oleh izin pada direktori induknya, di mana pemetaan nama-ke-ruang data dipertahankan.

Bit eksekusi memungkinkan file untuk dieksekusi. Ada dua jenis file yang dapat dieksekusi: biner, yang dijalankan CPU secara langsung, dan skrip, yang harus ditafsirkan oleh program seperti shell atau Python.Secara konvensi, skrip dimulai dengan baris **shebang** yang memberitahukan kernel penerjemah mana yang akan digunakan.

```bash
#!/usr/bin/perl
```

File yang dapat dieksekusi non-biner yang tidak menentukan interpreter diasumsikan sebagai skrip **sh**.

Kernel memahami sintaks *#!* (shebang) dan menindaklanjutinya secara langsung. Namun, jika interpreter tidak ditentukan secara lengkap dan benar, kernel akan menolak file tersebut.Shell kemudian mengambil alih dan mencoba menginterpretasikan berkas tersebut sebagai sebuah skrip shell.

Untuk sebuah direktori, execute bit (sering disebut search atau scan bit) mengijinkan direktori tersebut untuk dimasuki atau dilewati ketika pathname dievaluasi, tetapi tidak untuk diisikan isinya. Kombinasi bit read dan execute memungkinkan direktori dibaca dan isinya didaftar. Kombinasi bit write dan execute memungkinkan file dibuat, dihapus, dan diganti namanya di dalam direktori.

### The Setuid and Setgid Bits
Bit dengan nilai oktal 4000 dan 2000 masing-masing adalah bit setuid dan setgid. Ketika bit setuid disetel pada sebuah file, pemilik file untuk sementara diubah menjadi pemilik file ketika file tersebut dieksekusi. Ketika bit setgid di-set pada sebuah berkas, grup berkas untuk sementara diubah menjadi grup berkas ketika berkas tersebut dieksekusi.

Ketika disetel pada direktori, bit setgid menyebabkan file yang baru dibuat di dalam direktori mengambil kepemilikan grup direktori, bukan grup default pengguna yang membuat file. Hal ini memudahkan untuk berbagi file di antara sekelompok pengguna.

### The Sticky Bit
Bit dengan nilai oktal 1000 adalah bit lengket. Ketika ditetapkan pada direktori, bit sticky mencegah pengguna menghapus atau mengganti nama file yang bukan miliknya. Hal ini berguna untuk direktori seperti **/tmp** yang digunakan bersama oleh banyak pengguna.

### ls: list and inspect files
Perintah **ls** mencantumkan daftar file dan direktori. Perintah ini juga dapat digunakan untuk memeriksa atribut file dan direktori.

Opsi **-l** menyebabkan **ls** menampilkan format panjang, yang meliputi mode file, jumlah tautan keras ke file, pemilik file, grup file, ukuran file dalam byte, waktu modifikasi file, dan nama file.

Semua direktori memiliki paling tidak dua hard link: satu dari direktori itu sendiri (entri **.**) dan satu dari direktori induknya (entri **..**).

Keluaran **ls** sedikit berbeda untuk file perangkat. Sebagai contoh:

```bash
$ ls -l /dev/tty0
crw--w---- 1 root tty 4, 0 Mar  1  2020 /dev/tty0
```

Tanda **c** di awal baris menunjukkan bahwa file tersebut adalah file perangkat karakter. **4, 0** di akhir baris adalah nomor perangkat mayor dan minor.

### chmod: change permissions
Perintah **chmod** mengubah mode sebuah file. Anda dapat menggunakan notasi oktal atau notasi simbolik.

![permissions-encoding](./data/permissions-encoding.png)
Contoh-contoh dari sintaks mnemonic chmod:

| Penentu   | Arti                                                                |
| ----------| ------------------------------------------------------------------- |
| u+w       | Menambahkan izin tulis untuk pemilik berkas |
| ug=rw,o=r | Memberikan izin r/w kepada pemilik dan grup, dan izin r kepada orang lain
| a-x       | Menghapus izin eksekusi untuk semua pengguna
| ug=srx, o=| Mengatur setuid, setgid, dan bit lengket untuk pemilik dan grup (r/x) | | a-x | Mengatur bit setuid, setgid, dan bit lengket untuk pemilik dan grup (r/x)
| g=u       | Membuat izin grup sama dengan pemilik |

**Kiat**: Anda juga dapat menentukan mode yang akan ditetapkan dengan menyalin mode dari berkas lain dengan opsi **--referensi**. (contoh: **chmod --reference = file sumber file target**)
