# Aplikasi Web "ACTUAL"


## Sekilas Tentang Actual

Actual adalah alat keuangan pribadi lokal yang berbasis pada penganggaran zero-sum. Alat ini mendukung sinkronisasi lintas perangkat, aturan khusus, impor transaksi manual (dari berkas QIF, OFX, dan QFX), dan sinkronisasi otomatis opsional dengan banyak bank.


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
    
2. Instalasi Actual. Terdapat dua cara untuk melakukan instalasi, yaitu menggunakan clone repository atau docker image.
   
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
   - Pilih konfigurasi yang ada atau buat konfigurasi baru.

     ![image](https://github.com/user-attachments/assets/464ef244-80fe-4b3d-8d62-27726813565d)

   - Pilih akun Google yang akan digunakan dan pilih proyek dari daftar proyek yang tersedia.
     
     ![image](https://github.com/user-attachments/assets/0b657104-19a7-4393-bf06-378077b42890)

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

## Cara Pemakaian

### Penjelasan Fitur Secara General

#### 1. Tampilan Utama
Antarmuka pengguna dibagi menjadi tiga bagian:

1. Sidebar di sebelah kiri, yang memudahkan pengguna untuk beralih antara berbagai tampilan seperti Budget, Reports, dan Schedules.
2. Status server dan sinkronisasi (jika digunakan) ditampilkan di sudut kanan atas.
3. Konten utama antarmuka tergantung pada fitur yang dipilih di sidebar. Gambar ini menunjukkan contoh tampilan Budget.

![image](https://github.com/user-attachments/assets/0a58abfe-ca6d-4bd0-8a8e-be977df9c24d)

#### Sisi Kanan Atas
- Ikon mata akan mengaburkan angka-angka di layar, berguna jika perlu melaporkan bug kepada tim Actual sambil menjaga kerahasiaan data anggaran.
- Ikon sinkronisasi akan menyinkronkan file lokal dengan server.
- "Server online" menunjukkan status koneksi. Jika tidak terhubung, teks ini akan berubah menjadi Server offline. Teks ini memungkinkan pengguna untuk mengganti kata sandi, keluar dari server, atau mengubah URL server.

#### Sidebar
Dari sidebar, akses semua fitur Actual dengan mudah.
- Fitur utama seperti Budget, Reports, dan Schedules dapat diakses langsung. 
- Fitur yang jarang digunakan, seperti mengelola Payees dan Rules, dapat ditemukan di bawah menu More, bersama dengan pengaturan perangkat lunak.
- Sidebar juga menampilkan semua akun, baik yang berada di dalam anggaran (on-budget) maupun di luar anggaran (off-budget), dan akun-akun ini akan muncul dalam daftar di bawah kategori yang sesuai, seperti On Budget.

#### 2. Budget
Tampilan ini memungkinkan pengguna untuk mengelola anggaran di berbagai bulan. 

![image](https://github.com/user-attachments/assets/31e4aeb2-a154-4eef-b8ba-5816276967bd)
Jumlah bulan yang ditampilkan di layar bisa disesuaikan dengan klik ikon kalender di sudut kiri atas, pilih jumlah bulan yang ingin ditampilkan, lalu tentukan bulan mana saja yang akan ditampilkan sesuai kebutuhan.

#### Month Header

Di bagian atas setiap bulan, terdapat beberapa opsi dalam antarmuka pengguna:
- Ikon catatan memungkinkan untuk menambahkan catatan. Actual sepenuhnya mendukung Markdown, dan catatan akan dirender sesuai format Markdown saat kursor berada di atasnya.
- Header dapat diminimalkan dengan mengklik ikon chevron (dua panah ke atas).
- Ikon tiga titik vertikal memberikan akses ke fungsi-fungsi berikut untuk kategori anggaran bulan yang tercantum:
    - Salin anggaran bulan sebelumnya.
    - Atur anggaran ke nol.
    - Atur anggaran berdasarkan rata-rata 3 bulan terakhir.

#### Budget Table
##### Sisi Kiri - Bagian Category
- Bagian Budget Detail menampilkan semua kategori beserta kelompoknya. Pada gambar, terlihat dua kelompok kategori pengeluaran: Usual Expenses dan Bills. Kelompok kategori dapat diminimalkan, seperti yang terlihat pada Usual Expenses.
- Menu dropdown memungkinkan untuk menambahkan kategori baru, mengaktifkan/menyembunyikan kelompok kategori, mengganti nama kelompok, atau menghapus kelompok kategori.
- Markdown juga didukung pada bagian ini saat pengguna menulis catatan

##### Sisi Kanan - Bagian Budget
Bagian ini adalah tempat utama pengguna dapat mengelola anggaran. Terdapat tiga kolom: Budgeted, Spent, dan Balance.
- Budgeted menunjukkan jumlah uang yang dialokasikan untuk kategori tersebut di bulan itu.
- Spent menampilkan total pengeluaran selama bulan tersebut.
- Balance adalah selisih antara kolom Budgeted dan Spent, ditambah sisa dari bulan sebelumnya (sebagai aturan umum).

Kolom Budgeted adalah tempat mengelola anggaran. Angka dapat dimasukkan secara manual, atau menggunakan dropdown untuk mengisi anggaran berdasarkan salinan dari anggaran bulan sebelumnya, rata-rata 3 atau 6 bulan terakhir, hingga rata-rata tahunan.

#### 3. Akun Terdaftar
Tampilan ini memungkinkan untuk mengelola transaksi di suatu akun.

![image](https://github.com/user-attachments/assets/294aea74-d8e3-4768-a87c-fff966b18e29)

##### Header Akun
- Di bawah nama akun, saldo saat ini ditampilkan. Hijau menunjukkan saldo positif, sedangkan merah menunjukkan saldo negatif. Bagian atas juga menyediakan akses ke beberapa fungsi seperti mengimpor transaksi, menambahkan transaksi secara manual, dan memfilter transaksi yang ditampilkan. Di sebelah kanan terdapat kolom pencarian untuk dengan cepat mencari semua bidang.
- Transaksi dapat dipecah menjadi lebih dari satu kategori. Klik panah expand/contract untuk menampilkan atau menyembunyikan transaksi yang terpecah.
- Tiga titik horizontal memungkinkan berbagai manipulasi akun, seperti rekonsiliasi, mengekspor akun, menutup, dan menautkan akun untuk transaksi bank otomatis. Di sini juga terdapat opsi untuk menampilkan saldo berjalan akun. 
- Import memungkinkan untuk mengimpor transaksi dari file CSV, QIF, OFX, dan QFX.
- Untuk menambahkan transaksi secara manual, klik Add New. 

##### Filter Transaksi
![image](https://github.com/user-attachments/assets/aa82abc5-d8ca-42e1-bda4-1209ee54d04f)

Filter memungkinkan memfilter berdasarkan semua bidang. Pada gambar, dapat dilihat  pencarian di mana Category adalah Food. Jika sering menggunakan filter yang sama, filter dapat disimpan dengan mengklik dropdown Unsaved filter di sisi kanan header.

Saat memilih transaksi di sebelah kanan saldo akun, saldo dari transaksi yang dipilih akan muncul di sebelah kanan saldo akun tersebut. Jumlah transaksi yang dipilih ditampilkan dalam kotak merah. Mengklik dropdown ini memungkinkan menjalankan berbagai perintah pada transaksi yang dipilih.

#### 4. Laporan
Tampilan ini memberikan akses ke dua laporan bawaan, yaitu Net Worth dan Cash Flow, serta laporan khusus yang dibuat sendiri.Semua laporan ditampilkan dalam versi kecil. 

![image](https://github.com/user-attachments/assets/d5df80ae-346b-4d2e-9724-0d58b4b0f07c)

Net worth mencerminkan keseimbangan keseluruhan semua akun dari waktu ke waktu. Nilainya dihitung dengan mengurangi total utang dari nilai keseluruhan uang tunai dan aset, termasuk semua investasi. Pada dasarnya, net worth adalah jumlah yang akan dimiliki jika semua aset dijual dan semua utang dilunasi.

Cash flow melacak pengeluaran dari waktu ke waktu dengan fokus pada akun-akun yang dianggarkan dan menampilkan saldo mereka. Laporan ini menyajikan visualisasi terpisah untuk pendapatan dan pengeluaran, memberikan gambaran cepat tentang bagaimana kedua faktor ini memengaruhi uang yang tersedia. Dengan menganggap akun yang dianggarkan sebagai "uang tunai yang tersedia", cash flow secara jelas menunjukkan fluktuasi dana yang tersedia.

![image](https://github.com/user-attachments/assets/784d9b3b-325f-4e49-9344-e11bfbb56c3f)

Laporan khusus di Actual dirancang untuk memberikan semua informasi yang dibutuhkan terkait kebiasaan pengeluaran dan pendapatan. Pada gambar, laporan mencakup Usual Expenses selama enam bulan terakhir. Satu laporan disajikan dalam bentuk grafik atau tabel. 

#### 5. Transaksi Terjadwal (Scheduled Transactions)
Fitur lainnya adalah transaksi terjadwal dimana pengguna bisa menambahkan jadwal yang diusulkan berdasarkan data yang tersedia. 

Penjadwalan bisa dilakukan berdasarkan berbagai faktor berikut:
1. Diatur sebagai penjadwalan berulang atau hanya sekali
2. Dapat dimasukkan secara ototmatis ke dalam akun yang terdaftar atau pilih secara manual
3. Pilihan untuk dimasukkan beberapa hari tertentu dalam sebulan
4. Pilihan untuk menentukan frekuensi pembayaran, seperti tiap bulan atau lainnya

   ![image](https://github.com/user-attachments/assets/f8bd9804-39de-4474-b4b7-d764826a4815)

#### 6. Manajemen Penerima Pembayaran (Payees Management)
Tampilan ini memungkinkan untuk mengelola penerima pembayaran dimana fitur ini dapat memperlihatkan penerima pembayaran mana yang ada di sistem.

![image](https://github.com/user-attachments/assets/956376e9-722b-4863-9c5d-567ae220bf6c)

Fitur ini juga dapat menghapus dan menggabungkan penerima pembayaran sesuai keinginan. 

### Tahapan Penggunaan untuk Pemula

#### 1. Membuat Akun
Untuk pertama kali membuka web pengguna akan ditunjukkan untuk membuat akun.

![image](https://github.com/user-attachments/assets/d613792f-d388-4458-ae01-e269224c8d26)

Pilih untuk membuat local akun.

![image](https://github.com/user-attachments/assets/cf150591-8aa0-4936-927a-cd8ebf8894c8)

Masukkan name dan balance sesuai yang diinginkan.

![image](https://github.com/user-attachments/assets/e56d8a4a-778c-41a0-adb7-e1aa07dd14cc)

Tekan create dan akun berhasil dibuat.

![image](https://github.com/user-attachments/assets/d353d10f-fb00-4203-aa17-5708b77c4f55)

#### 2. Menghapus dan Mengganti Nama Akun
Pengguna dapat menghapus atau mengganti nama akun sebagai berikut.

![image](https://github.com/user-attachments/assets/64621a36-b05d-4e65-824f-c04b3d93b3b6)

#### 3. Pengaturan Tampilan
Pada halaman settings, pengguna dapat mengubah beberapa hal terkait tampilan, seperti format waktu, tema halaman, format nomor, dan lainnya.

![image](https://github.com/user-attachments/assets/8b3d70b3-4c5a-4cc8-a580-30d289188c09)

#### 4. Mulai membaut budget
Pengguna dapat membuat manajemen budget perbulan secara manual dengan menekan icon Add New atau dengan menggunakan fitur import excel.

![image](https://github.com/user-attachments/assets/fa1bef9b-49fa-4487-a8cd-4937955f7362)

Setelah membuat budget perbulan, pengguna dapat melihat tampilan rincian pada halaman Budget. Pada halaman tersebut pengguna dapat melihat rincian pengeluaran seperti Food, availaible funds bulan ini, jumlah kelebihan pengeluaran, dan sisa budget untuk bulan depan.

![image](https://github.com/user-attachments/assets/ec666553-8bc0-49bd-b547-1fc1547ebbe2)

#### 5. Membuat Kategori (Category)
Pengguna juga dapat membuat category baru sesuai keinginan dengan menekan titik 3 atau arrow yang berada disamping kolom category.

![image](https://github.com/user-attachments/assets/b3465794-1bd5-461c-9bc9-b74d3e30ce3a)

#### 6. Halaman Report
Halaman untuk melihat visualisasi data budget pengguna dari bulan ke bulan.

![image](https://github.com/user-attachments/assets/aca24599-e688-4e05-8be1-c85337e8d762)

#### 7. Halaman Schedules
Halaman untuk membuat kegiatan transaksi yang sering dilakukan tiap bulan. Sebagai contoh transaksi untuk pembayaran kesehatan setipa tanggal 15.

![image](https://github.com/user-attachments/assets/77b3272c-8bfe-4f88-b382-2282da4a2ef1)

#### 8. Halaman Payees
Halaman untuk membuat manajemen pembayaran. 

![image](https://github.com/user-attachments/assets/110fe4b4-9534-4bdd-a5aa-fe058e75258c)

#### 8. Halaman Rules
Halaman untuk membuat peraturan baru untuk manajemen pembayaran atau pemasukan.

![image](https://github.com/user-attachments/assets/6242969a-01a2-40d5-a9dc-b5866032f8bb)

![image](https://github.com/user-attachments/assets/69b71138-fb43-4b1e-89ce-c1edad265e65)

## Pembahasan

###Tentang Aplikasi Actual

####Kelebihan
- Aplikasi ini memiliki berbagai fitur untuk mendukung pengguna dapat lebih fleksibel dalam manajemen budget.
- Memiliki desain yang responsive dan dapat digunakan dengan device apapun (Mobile atau PC).
- Terdapat fitur import excel untuk memudahkan pengguna
- Keamanan dan privasi data pengguna lebih terjamin karena data disimpan secara lokal, bukan di cloud. Sehingga pengguna tidak perlu khawatir data pribadi akan diakses oleh pihak ketiga.
- Meskipun berbasis lokal, Actual mendukung sinkronisasi multi-perangkat, sehingga pengguna tetap dapat mengakses dan mengelola keuangan di berbagai perangkat tanpa harus bergantung pada cloud.
####Kekurangan
- Dibandingkan dengan aplikasi keuangan lain yang lebih komersial, Actual mungkin kurang dalam fitur lanjutan seperti pelacakan investasi atau laporan analitis yang lebih kompleks.
- Meskipun sinkronisasi otomatis dengan bank tersedia, ini adalah fitur opsional yang mungkin tidak mendukung semua bank atau mungkin memerlukan biaya tambahan. Pengguna dengan bank yang tidak didukung harus melakukan impor transaksi secara manual.
  
###Perbandingan dengan aplikasi web lain yang sejenis

####Actual vs YNAB(You Need A Budget):
- Keduanya menggunakan zero-sum budgeting, namun YNAB menyediakan lebih banyak panduan bagi pengguna baru.
- Actual menggunakan pendekatan local-first, sedangkan YNAB menggunakan cloud.
- Actual gratis dan open-source, YNAB berbayar dengan langganan bulanan.
- YNAB mendukung lebih banyak bank secara otomatis dibandingkan Actual.
####Actual vs Mint:
- Mint berfokus pada pelacakan pengeluaran, sementara Actual pada pengelolaan anggaran ketat.
- Mint mendukung sinkronisasi otomatis dengan banyak bank, Actual terbatas.
- Mint memiliki fitur pelacakan investasi dan peringatan anggaran, sedangkan Actual lebih minimalis.
####Actual vs Personal Capital:
- Personal Capital lebih berfokus pada pelacakan kekayaan dan investasi, sedangkan Actual untuk pengelolaan anggaran harian.
- Personal Capital unggul dengan alat investasi, Actual tidak menyediakan alat investasi.
- Actual lebih aman dengan data lokal, Personal Capital menggunakan cloud yang lebih rentan.

## Referensi

https://actualbudget.org/docs/getting-started/starting-fresh
