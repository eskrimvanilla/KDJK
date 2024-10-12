# Aplikasi Web "ACTUAL"


## Sekilas Tentang Actual

Actual adalah alat keuangan pribadi berbasis lokal yang berbasis pada penganggaran zero-sum. Alat ini mendukung sinkronisasi lintas perangkat, aturan khusus, impor transaksi manual (dari berkas QIF, OFX, dan QFX), dan sinkronisasi otomatis opsional dengan banyak bank.


## Instalasi

#### Kebutuhan Sistem :
- Linux CLI
- Php 5.3+
- Node.js v18 atau versi yang lebih tinggi
- Web server (disini kami menggunakan Google Cloud Platform)

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
    $ git clone https://github.com/actualbudget/actual-server.git
    ```
    
    Pull dari docker image:
    ```
    $ docker pull actualbudget/actual-server
    ```

3. Konfigurasi GCP :
    Jalankan perintah berikut untuk login ke akun Google Cloud:
    ```
    $ gcloud auth login
    ```
    Selanjutnya akan diarahkan untuk login menggunakan akun Google Cloud. Setelah login, inisialisasi konfigurasi dengan perintah berikut untuk terhubung ke proyek:
    ```
    $ gcloud init
    ```
    Untuk langkah konfigurasi, pilih opsi sesuai dengan yang diinginkan.
   - Pilih konfigurasi yang ada atau buat konfigurasi baru
     Foto1
   - Pilih akun Google yang akan digunakan dan pilih proyek dari daftar proyek yang tersedia
     Foto2
4. Setelah menghubungkan Linux CLI dengan GCP, langkah berikutnya adalah membuat repository baru untuk Artifact Registry di GCP.

   Klik tombol + Create Repository yang terdapat di halaman Artifact Registry pada dashboard Google Cloud.
   
   ![image](https://github.com/user-attachments/assets/f22e14e9-2f66-4e09-ba0f-25563ff16e57)
   
   Buat nama repository sesuai keinginan. Pilih Docker sebagai format, atau gunakan format nama sebagai NAME.gcr.io agar format otomatis menjadi Docker. Pilih lokasi penyimpanan repository dengan opsi Region atau Multi-region untuk memastikan keamanan data dan meminimalkan risiko kehilangan data.
   
   ![image](https://github.com/user-attachments/assets/7472f350-5e86-409d-b6dd-6212e29a9efc)
   
   Sebelum melakukan push ke Artifact Registry, pastikan untuk membuat **Service Account** dengan peran IAM (IAM roles) berikut:
   - Artifact Registry Administrator
   - Artifact Registry Create-on-Push Repository Administrator
   - Storage Admin
     
     ![image](https://github.com/user-attachments/assets/cc608fc7-6312-44f5-a8c2-cc7126d65785)
     
     ![image](https://github.com/user-attachments/assets/a14bc9f0-4cf6-44d7-8fe5-becd16065791)

   Langkah selanjutnya adalah mengekspor kredensial dengan cara berikut:
   - Buka Service Accounts di menu IAM & Admin.
   - Klik pada Service Account yang telah dibuat.
   - Pada bagian Keys, klik Add Key, lalu pilih Create New Key.
   - Pilih Key Type sebagai JSON dan simpan file yang diunduh untuk digunakan nanti.
     
     ![image](https://github.com/user-attachments/assets/d8ddc6f6-2bf2-4fa7-a92c-e373e41ebd54)
5. Pindah ke CLI Linux dan atur kredensial aplikasi Google Cloud dengan perintah berikut:
    ```
    $ export GOOGLE_APPLICATION_CREDENTIALS="/path/to/file.JSON"
    ```
    Setelah itu, buat image Docker dari file yang akan di-push. 

    Langkah pertama, menandai (tag) image tersebut dengan perintah berikut:
    ```
    $ docker tag actualbudget/actual-server kdjk/pure-nectar-413300/actual
    ```
    Penjelasan:
    - ```actualbudget/actual-server``` adalah nama folder berisi kode yang akan di-push.
    - ```kdjk``` adalah nama repository di Artifact Registry.
    - ```pure-nectar-413300``` adalah ID dari project di Google Cloud.
    - ```actual``` adalah nama image yang sedang dibuat.
    
    Langkah selanjutnya, push image tersebut ke Artifact Registry menggunakan perintah:
    ```
    $ docker push kdjk/pure-nectar-413300/actual
    ```

    Bila anda tidak bisa melakukan push ke artifact registry, maka jalankan perintah berikut sebelum perintah push:
    ```
    $ gcloud auth print-access-token | docker login -u oauth2accesstoken --password-stdin https://asia-southeast2-docker.pkg.dev
    ```     
    Ganti asia-southeast2-docker.pkg.dev sesuai dengan region yang digunakan.
    
    Setelah proses push selesai, image akan muncul di Artifact Registry sesuai dengan detail yang telah ditentukan.
    
   ![image](https://github.com/user-attachments/assets/efd2c65a-7bb7-4806-ac83-ed02d26f5c09)
   
    Langkah terakhir, web dapat dideploy menggunakan Cloud Run dengan cara klik **Deploy Container**, pilih **Service**, lalu pilih **Container Image URL** yang sesuai.
    
   ![image](https://github.com/user-attachments/assets/4917e84c-261e-4cbb-8cf0-61952b195ce1)
   
    Nama Service akan terisi secara otomatis. Pilih region sesuai keinginan, disarankan untuk memilih yang paling dekat, yaitu ```asia-southeast2```. Biarkan opsi lainnya pada pengaturan default, lalu klik Create.
    
   ![image](https://github.com/user-attachments/assets/ceb0af6f-34b4-4825-859c-74ecf80eafae)
   
   Tunggu hingga proses deployment selesai. Setelah itu, klik nama Service yang sudah dibuat. URL aplikasi akan muncul di bagian atas.
   
   ![image](https://github.com/user-attachments/assets/50c70126-70bc-453b-b63a-32936f28a16c)

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

### 1. Tampilan Utama
Antarmuka pengguna dibagi menjadi tiga bagian:

1. Sidebar di sebelah kiri, yang memudahkan pengguna untuk beralih antara berbagai tampilan seperti Budget, Reports, dan Schedules.
2. Status server dan sinkronisasi (jika digunakan) ditampilkan di sudut kanan atas.
3. Konten utama antarmuka tergantung pada fitur yang dipilih di sidebar. Gambar ini menunjukkan contoh tampilan Budget.

![image](https://github.com/user-attachments/assets/0a58abfe-ca6d-4bd0-8a8e-be977df9c24d)

#### Sisi Kanan Atas
1. Ikon mata akan mengaburkan angka-angka di layar, berguna jika perlu melaporkan bug kepada tim Actual sambil menjaga kerahasiaan data anggaran.
2. Ikon sinkronisasi akan menyinkronkan file lokal dengan server.
3. "Server online" menunjukkan status koneksi. Jika tidak terhubung, teks ini akan berubah menjadi Server offline. Teks ini memungkinkan pengguna untuk mengganti kata sandi, keluar dari server, atau mengubah URL server.

#### Sidebar

### 2. Budget
Tampilan ini memungkinkan pengguna untuk mengelola anggaran di berbagai bulan. 

![image](https://github.com/user-attachments/assets/31e4aeb2-a154-4eef-b8ba-5816276967bd)
Jumlah bulan yang ditampilkan di layar bisa disesuaikan dengan klik ikon kalender di sudut kiri atas, pilih jumlah bulan yang ingin ditampilkan, lalu tentukan bulan mana saja yang akan ditampilkan sesuai kebutuhan.

#### Month Header

Di bagian atas setiap bulan, terdapat beberapa opsi dalam antarmuka pengguna:
1. Ikon catatan memungkinkan untuk menambahkan catatan. Actual sepenuhnya mendukung Markdown, dan catatan akan dirender sesuai format Markdown saat kursor berada di atasnya.
2. Header dapat diminimalkan dengan mengklik ikon chevron (dua panah ke atas).
3. Ikon tiga titik vertikal memberikan akses ke fungsi-fungsi berikut untuk kategori anggaran bulan yang tercantum:
    - Salin anggaran bulan sebelumnya.
    - Atur anggaran ke nol.
    - Atur anggaran berdasarkan rata-rata 3 bulan terakhir.

#### Budget Table
##### Sisi Kiri - Bagian Category
1. Bagian Budget Detail menampilkan semua kategori beserta kelompoknya. Pada gambar, terlihat dua kelompok kategori pengeluaran: Usual Expenses dan Bills. Kelompok kategori dapat diminimalkan, seperti yang terlihat pada Usual Expenses.
2. Menu dropdown memungkinkan untuk menambahkan kategori baru, mengaktifkan/menyembunyikan kelompok kategori, mengganti nama kelompok, atau menghapus kelompok kategori.
4. Markdown juga didukung pada bagian ini saat pengguna menulis catatan

##### Sisi Kanan - Bagian Budget
Bagian ini adalah tempat utama pengguna dapat mengelola anggaran. Terdapat tiga kolom: Budgeted, Spent, dan Balance.
1. Budgeted menunjukkan jumlah uang yang dialokasikan untuk kategori tersebut di bulan itu.
2. Spent menampilkan total pengeluaran selama bulan tersebut.
3. Balance adalah selisih antara kolom Budgeted dan Spent, ditambah sisa dari bulan sebelumnya (sebagai aturan umum).

Kolom Budgeted adalah tempat mengelola anggaran. Angka dapat dimasukkan secara manual, atau menggunakan dropdown untuk mengisi anggaran berdasarkan salinan dari anggaran bulan sebelumnya, rata-rata 3 atau 6 bulan terakhir, hingga rata-rata tahunan.

## Pembahasan

- Pendapat anda tentang aplikasi web ini
    - kelebihan
    - kekurangan
- Bandingkan dengan aplikasi web lain yang sejenis


## Referensi

Cantumkan tiap sumber informasi yang anda pakai.
