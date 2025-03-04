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

```bash
mount /dev/sda4 /users
```

Linux has a lazy unmount option (**umount -l**) that removes a filesystem from the naming hierarchy but does not truly unmount it until it is no longer in use.

**umount -f** is a forceful unmount, which is useful when the filesystem is busy.

Instead of reaching for **umount -f**, you can use **lsof** or **fuser** to find out which processes are using the filesystem and then shut them down.

Example:

```bash

abdou@debian:~$ lsof /home/abdou

COMMAND   PID USER   FD   TYPE DEVICE SIZE/OFF   NODE NAME
bash     1000 abdou  cwd    DIR    8,1     4096  131073 /home/abdou
bash     1000 abdou  rtd    DIR    8,1     4096  131073 /home/abdou
bash     1000 abdou  txt    REG    8,1   103752  131072 /bin/bash
bash     1000 abdou  mem    REG    8,1  1848400  131074 /lib/x86_64-linux-gnu/libc-2.28.so
bash     1000 abdou  mem    REG    8,1   170864  131075 /lib/x86_64-linux-gnu/ld-2.28.so
code     1234 abdou  cwd    DIR    8,1     4096  131073 /home/abdou
msedge   5678 abdou  cwd    DIR    8,1     4096  131073 /home/abdou