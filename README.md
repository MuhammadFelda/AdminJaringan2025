# AdminJaringan2025
<p align="center">hello World</p>
<p>A. Analisa File http.cap dengan Wireshark :</p>
<ol>
  <li>
      Versi HTTP yang digunakan
      <img src="./img/HTTP-Version.png">
      <ul>
        <li>
            <p>Pada gambar di atas dapat diketahui bahwa versi HTTP yang digunakan adalah versi HTTP/1.1</p>
        </li>
        <li>
            <p>Untuk dapat mengetahui versi HTTP melalui wireshark dengan menekan packet nomor 4, kemudian klik kanan dan pilih opsi follow dan HTPP streams. Untuk melihat HTTP version dapat dilihat pada bagian <i>client</i> dan <i>server</i> pada bagian paling atas</p>
        </li>
      </ul>
  </li>
</ol>
<p>B. Deskripsi Gambar Pada Slide</p>
<img src="./img/Type-of-Data-Deliveries.png">
<p>Pada gambar di atas dapat dijelaskan bahwa :</p>
<ol>
  <li>
    Node to Node (Data Link Layer)
    <p>Pengiriman data terjadi antara dua perangkat fisik yang saling terhubung, seperti komputer atau switch, di dalam jaringan lokal</p>
  </li>
  <li>
    Host to Host (Network Layer)
    <p>Pengiriman data antara dua perangkat di luar jaringan lokal, seperti internet. Proses ini menggunakan pengalamatan dan routing data dari host ke host lainnya.</p>
  </li>
  <li>
    Process to Process (Transport Layer)
    <p>Pengiriman data terjadi antara aplikasi yang berjalan pada dua sistem yang berbeda. Protokol transport, seperti TCP dan UDP, mengatur jalur komunikasi data antara dua proses di dua host yang bebeda, seperti server dan clien</p>
  </li>
</ol>

<p>C. Rangkuman Tahapan Komunikasi Menggunakan TCP</p>
<ol>
  <li>
      Connection Establishment Using Three-Way Handshaking
      <img src="./img/connection-establishment-using-three-way-handshaking.png">
      <p></p>
  </li>
  <li>
      Data Transfer
      <img src="./img/Data-Transfer.png">
      <p></p>
  </li>
  <li>
      Connection Termination Using Three-Way Handshaking
      <img src="">
      <p></p>
  </li>
  <li>
      Half-close
      <img src="">
      <p></p>
  </li>
</ol>