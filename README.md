# Aplikasi Web "ACTUAL"


## Sekilas Tentang Actual

Actual adalah alat keuangan pribadi berbasis lokal yang berbasis pada penganggaran zero-sum. Alat ini mendukung sinkronisasi lintas perangkat, aturan khusus, impor transaksi manual (dari berkas QIF, OFX, dan QFX), dan sinkronisasi otomatis opsional dengan banyak bank.


## Instalasi

#### Kebutuhan Sistem :
- Linux CLI
- Php 5.3+
- Node.js v18 atau versi yang lebih tinggi
- Web server (GCP)
- Docker dan Docker compose

#### Proses Instalasi :
1. Pastikan sudah memiliki docker dan docker compose dengan menjalankan code berikut. 
    ```
    $ docker -v
    $ docker-compose -v
    ```
    Jika docker belum terinstal, jalankan kode berikut.
    ```
    $ sudo snap install docker  
    $ sudo apt  install docker-compose
    ```
    
2. Instalasi Actual. Terdapat dua cara untuk melakukan instalasi, yaitu menggunakan clone repository atau docker image
   
    Clone repository :
    ```
    $ git clone https://github.com/actualbudget/actual-server.git\
    ```
    
    Pull dari docker image:
    ```
    $ docker pull actualbudget/actual-server
    ```

4. Jalankan command berikut untuk terhubung ke GCP:
    ```
    $ gcloud auth login
    ```
    Selanjutnya akan diarahkan untuk login menggunakan akun Google Cloud. Setelah login, gunakan perintah berikut untuk terhubung ke proyek:
    ```
    $ gcloud init
    ```
    ![image](https://github.com/user-attachments/assets/05d2af1e-0746-4aac-9ed8-ae2f976b9718)

## Konfigurasi (opsional)

Setting server tambahan yang diperlukan untuk meningkatkan fungsi dan kinerja aplikasi, misalnya:
- batas upload file
- batas memori
- dll

Plugin untuk fungsi tambahan
- login dengan Google/Facebook
- editor Markdown
- dll


##  Maintenance (opsional)

Setting tambahan untuk maintenance secara periodik, misalnya:
- buat backup database tiap pekan
- hapus direktori sampah tiap hari
- dll


## Otomatisasi (opsional)

Skrip shell untuk otomatisasi instalasi, konfigurasi, dan maintenance.


## Cara Pemakaian

- Tampilan aplikasi web
- Fungsi-fungsi utama
- Isi dengan data real/dummy (jangan kosongan) dan sertakan beberapa screenshot


## Pembahasan

- Pendapat anda tentang aplikasi web ini
    - kelebihan
    - kekurangan
- Bandingkan dengan aplikasi web lain yang sejenis


## Referensi

Cantumkan tiap sumber informasi yang anda pakai.
