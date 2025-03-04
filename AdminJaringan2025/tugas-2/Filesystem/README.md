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