# Predictive-Analysis-IHSG-TimeSeries

## A. Domain Proyek
Analisis dan Prediksi IHSG Menggunakan Pendekatan Time Series
### Latar Belakang
<p align="justify"> 
  IHSG atau Indeks Harga Saham Gabungan adalah sebuah indeks yang menampilkan rata-rata pergerakan seluruh saham di Bursa Efek Indonesia (BEI) [1]. Indeks tersebut memiliki fungsi dapat sebagai pergerakan saham di pasar modal, lalu menganalisis portfolio jika investor ingin menanamkan modal di Indonesia, dan salah satu tolok ukur melihat pertumbuhan ekonomi. Dengan adanya IHSG sebagai salah satu tolok ukur pertumbuhan ekonomi, pada bulan Maret 2025 IHSG mengalami penurunan, masyarakat Indonesia serentak membicarakan hal tersebut dan mengklaim “Indonesia tidak baik-baik saja”.
<p align="justify"> 
  Adanya IHSG sebagai indikator salah satu pertumbuhan ekonomi didukung oleh beberapa penelitian dimana Nilai tukar rupiah, inflasi, suku bunga, harga minyak dunia berpengaruh terhadap naik turunya IHSG [2] [3] [4]. Kemampuan dalam memprediksi pergerakan IHSG merupakan hal penting  untuk mengambil keputusan yang tepat. Salah satu yang digunakan untuk memprediksi IHSG adalah time series analysis. 
<p align="justify"> 
  Dengan demikian, proyek ini bertujuan untuk membuat model yang dapat memprediksi IHSG yang memanfaatkan data historis sehingga dapat memberikan gambaran awal mengenai pergerakan indeks IHSG.

## B. Business Understanding
### 1. Problem Statement
- Bagaimana membuat model machine learning yang dapat memprediksi IHSG di masa depan?
- Bagaimana teknik model yang paling baik untuk membuat model machine learning

### 2. Goals
- Membuat Model Machine learning untuk memprediksi IHSG di masa depan 
- Membuat teknik model yang paling baik untuk membuat model machine learning
  
### 3. Solution Statements
- Data IHSG dimuat lalu dibersihkan untuk mengetahui pola dalam data
- Data IHSG diproses menjadi time series dan dibagi dengan data training 80% dan validasi 20%
- Data diubah menjadi format window untuk mempelajari hgubungan antar waktu secara berurutan
- Dilakukan dua pendekatan dalam pengembangan model yaitu LSTM tanpa Tuning dan LSTM dengan Tuning
- Model dievaluasi dengan MAE dan kurva loss
- Hasil prediksi dalam bentuk grafik

## C. Load & Data Understanding
|Sumber data |https://id.investing.com/indices/idx-composite|
|----|----|
<p align="justify">
Dataset IHSG tersebut digunakan untuk analisis yang dimulai dari tahun 1993 - Mei 2025. Namun, dataset dipisahkan menjadi dua : data IHSG 1990-2010 dan data IHSG 2011 - Mei 2025. Oleh karena itu, kedua dataset IHSG digabungkan menjadi satu menggunakan concat.
<p align="justify">
Untuk mencapai tujuan analisis maka diperlukan pemahaman data, berikut informasi mengenai data IHSG 1993 - Mei 2025: 
  
**1. Mempunyai variable/kolom berjumlah 7, dengan fungsi setiap variabel berikut:**
  
|Variable |Deskripsi|
|----|----|
|Date| Tanggal perdagangan saham (format: MM/DD/YYYY), misalnya 09/28/2010 berarti 28 September 2010.|
|Price| Harga penutupan IHSG pada akhir hari perdagangan|
|Open| Harga penutupan IHSG pada akhir hari perdagangan |
|High| Harga tertinggi IHSG yang tercatat pada hari tersebut|
|Low| Harga terendah IHSG yang tercatat pada hari tersebut.|
|Vol.| Volume transaksi|
|Change  %	|Persentase perubahan harga IHSG dibandingkan dengan hari sebelumnya. Contoh: 0.13% berarti harga naik 0.13% dibanding hari sebelumnya|

**2. Mempunyai baris berjumlah 8500**

**3. Semua variabel bertipe data object**

**4. Mempunyai missing value pada variable Vol.**

**5. Tidak mempunyai duplikasi data**

## D. Data Preparation
<p align="justify">
Dalamm proses load dan understading Data sudah dilakukan penggabungan data IHSG versi 1990-2010 dan vers 2011 - Mei 2025 menggunakan Concat. Oleh karena itu, daalam data preparation akan menggunakan data_ihsg yang sudah digabungkan dari penggabungan data IHSG versi 1990-2010 dan versi 2011 - Mei 2025. Atau bisa inisalisasi ulang menggunakan concat
  <p align="justify">
Pada tahap Load dan Understanding Data, sudah mengetahui tuh apa saja yang harus dibersihkan, langkah selanjutnya adalah pembersihan data dan prepare data untuk modeling nanti.

**1. Cleaning Data**

Membuat fungsi cleaning untuk membersihkan data:
  <p align="justify">
- Tujuan pada analisis proyek ini adalah melihat pergerakan IHSG di masa depan berdasarkan Price atau Harga. Maka fitur selain Price dan Date akan dihapus
      <p align="justify">
- Tidak akan menghapus missing value pada karena variabel Date dan Price tidak ada yg kosong, melainkan yang kosong ada pada variabel Vol. dimana variabel Vol. akan dihapus

|Fungsi_Cleaning|Deskripsi|
|----|----|
|delete_columns(df, columns_to_delete)| Fungsi untuk menghapus kolom|
|convert_to_datetime(df, col_name)|Fungsi untuk mengubah tipe data menjadi datetime|
|convert_and_sort_datetime(df, col_name)|Fungsi untuk mengurutkan tanggal secara ascending|
|convert_price_to_float(df, col_name)|Fungsi untuk mengubah tipe data menjadi float|

<p align="justify">
Fungsi cleaning diatas digunakan untuk membersihkan data
<p align="justify">
- Variable Open, High, Low, Vol., dan Change % akan dihapus karena tidak akan dilakukan proses analisis pada proyek kali ini. Namun, Price akan dilakukan analisis, dimana Price aka dijadikan sebagai variabel yang akan memprediksi pergerakan IHSG.
<p align="justify">
- Lalu dilakukan konversi tipe data menjadi datetime pada variabel Date dan diurutkan

**2. Normalisasi Data**

<p align="justify">
Pada proyek ini, hanya 2 variabel yang akan digunakan untuk mencapai tujuan yaitu variabel Date dan Price. Tetapi, hanya variabel price yang akan dinormalisasikan menggunakan Min-Max Scaling agar nilai pada price dengan rentang 0 - 1.

**3. Mengubah Price dan Date menjadi Array**

Setelah dinormalisasi, data variabel price dan date dimuat dalam array untuk Splitting data dan bisa dimuat dalam model nanti.

**4. Memuat data train dan data validation**

<p align="justify">
Selanjutnya membagi dataset untuk proses latih dan uji model. train_series berisi 80% data untuk melatih model, sedangkan val_series berisi 20% data untuk menguji performa model pada data yang belum dilatih

**5. Membuat windowing**

<p align="justify">
Untuk memprediksi pergerakan IHSG pada time series perlu teknik windowing. Fungsi windowed_dataset_multi() pada analisis ini adalah untuk mempersiapkan data time series menjadi format yang dapat dilatih model dengan memotong data time series menjadi potongan kecil yang berisi data masa lalu dan data prediksi. Diperlukan parameter dalam pembuatan windowing, diantaranya:

|Parameter Windowing|Deskripsi|
|----|----|
|Window_size = 30|Untuk menentukan panjang window input data IHSG 30 hari ke belakang|
|Output_size = 60|Menentukan panjang prediksi nilai IHSG untuk 60 hari kedepan|
|Batch_size = 100|Sampel yang diproses dalam satu iterasi training|
|Shuffle_buffer = 1000|Mengacak data|

<p align="justify">
Setelah itu dimuat train_set sebagai dataset hasil windowing dari data train yang akan digunakan untuk melatih model. Lalu, val_set dataset hasil windowingg dari data validasi untuk mengukur performa model.

## E. Modeling 
<p align="justify">
Modeling adalah proses membangun dan melatih model machine learning untuk memahami pola data dan membuat prediksi. Dalam proyek ini, modeling digunakan agar bisa memprediksi nilai IHSG di masa depan dalam 60 hari berdasarkan data historis. Dalam proyek ini, dilakukan modeling dengan 2 cara yaitu menggunakan LSTM tanpa hyperparameter Tuning dan LSTM menggunakan hyperparameter Tuning

**1. LSTM Without Tuning**
<p align="justify">
Pada modeling pertama LSTM tanpa Tuning, dibangun dengan pendekatan berlapis. Lapisan pertama dengan 63 unit, lalu lapisan kedua 32 unit menerima output dari layer pertama. Lalu Dense digunakan 64 dan 32 untuk memproses output LSTM sebelum prediksi. Terakhir Dense 60 sebagai layer output akhir karena menghasilkan 60 nilai prediksi untuk 60 hari ke depan.
  <p align="justify">
Pada modeling pertama setelah membangun layer LSTM digunakan optimizer adam dengan learning rate cukup kecil untuk stabilan training. Loss function digunakan adalah mse dan metric untuk evaluasi yaitu mae. Digunakan juga Early Stopping dan Model Checkpoint untuk menghentikan training jika validasi loss tidak membaik dan menyimpan model.
    <p align="justify">
Langkah terakhir dilakukan pelatihan model dengan fit menggunakan train_set dan va_set. Lau Epochs sebanyak 100 putaran, tetapi bisa berhenti awal karena early stopping

**2. LSTM with Tuning**
<p align="justify">
Hyperparameter tuning adalah proses mencari kombinasi parameter terbaik untuk model machine learning agar performanya maksimal. Pada model kedua LSTM akan dikombinasikan dengan Tuning. Maka, dibut fungsi build_model() untuk membangun model LSTM dimana nilai hyperparameter bisa berubah dan diuji oleh alat tuning yaitu Keras Tuner. Lalu optimizer menggunakan Adam

|Layer|Jumlah Layer|
|----|----|
|LSTM layer pertama|32, 64, 96, 128|
|LSTM layer kedua|16, 32, 48, 64|
|Dense layer setelah LSTM|32, 64, 96, 128|

<p align="justify">
Setelah itu menjalankan tuner.search() untuk menjalankan proses hyperparameter terbaik untuk model yang dibuat fungs build_model(). Setelah itu, diambil konfigurasi terbaik dan menghasilkan hyperparameter terbaik yaitu
  
- Layer LSTM pertama menggunakan 32 neuron.
- Layer LSTM kedua menggunakan 16 neuron.
- Layer Dense menggunakan 32 neuron.
- Optimizer Adam menggunakan learning rate 0.001, yang lebih cepat daripada 0.0001, dan memberikan hasil terbaik.
  <p align="justify">
Setelah mengetahui konfigurasi terbaik dari tuning maka selanjutnya menggunakan konfigurasi terbaik tersebut untuk proses training

## F. Evaluasi dan Result
<p align="justify">
Setelah model selesai dilatih, langkah berikutnya adalah evaluasi performa model untuk mengetahui seberapa baik prediksi yang dihasilkan. Teknik Evaluasi model menggunakan dua cara:

### 1. Evaluasi
  
#### a. Curve Loss Function
<p align="justify">
Kurva loss function akan menunjukan peforma model selama pelatihan, dimana line pada kurva tersebut adalah sebuah epoch. Dengan kurva ini aka diketahui apakah model overfitting atau underfitting

#### b. MAE (Mean Absollute Error)
<p align="justify">
MAE dapat mengukur sebuah kesalahan antara nila prediksi dan nilai validasi. Jika nilai MAE semkakin mendekati 0 maka model semakin baik.

#### **Berikut Evaluasi dari Model yang telah di training:**

**1. LSTM without Tuning**
![image](https://github.com/user-attachments/assets/1634f845-9c9c-4cd3-8b5c-2ccc9532e8ed)


  **Analisis Grafik:**
    - Train dan validation loss sama sama turun dratis terus mendatar di nilai yang sangat kecil
    
    - Tidak ada gap besar
    
    - Validation loss sedikit lebih tinggi dari training loss, tapi polanya mengikuti dan stabil, tidak naik tajam di akhir epoch.

  **Analisis Score:**
    - nilai  loss dan mae pada training dan validasi sama sama kecil
    
    - mae validasi sedikit lebih tinggi dari training tapi gap nya tidak besar
    
    - Tidak ada tanda overfitting (yaitu training loss jauh lebih kecil dari validation loss).
    
    - Tidak ada tanda underfitting (yaitu loss tidak turun atau tetap tinggi).

  **Kesimpulan:**
    - Model LSTM Tidak Overfitting dan Underfitting
    
    - Model baik dan bisa digunakan untuk prediksi

**2. LSTM with Tuning**
![image](https://github.com/user-attachments/assets/7494eb68-13c8-46ad-97ae-f25f764e9ac7)

  **Analisis Grafik:**
    - Train dan validation loss sama sama turun dengan cepat
    
    - Tidak ada gap besar
    
    - Validation loss juga turun di awal, lalu fluktuasi dan lebih tinggi dari training loss.

  **Analisis Score:**
    - nilai  loss dan mae pada training dan validasi sama sama kecil
    
    - mae validasi sedikit lebih tinggi dari training tapi gap nya tidak besar
    
    - Tidak ada tanda overfitting (yaitu training loss jauh lebih kecil dari validation loss).
    
    - Validation MAE juga masih cukup rendah.

  **Kesimpulan:**
  
    - Model LSTM Tidak Overfitting dan Underfitting



**4. Perbandingan score MAE antara LSTM wihout Tunning & With Tunning**

![image](https://github.com/user-attachments/assets/4f96f5d5-5fc4-44d9-9ef9-5b68ca190a2b)

 <p align="justify">
Model LSTM with Tunning memiliki MAE yang lebih rendah dari LSTM without tuning baik dari train dan validation. maka LSTM with Tunning model yang paling baik untuk prediksi

### 2 Result
 <p align="justify">
Setelah mengetahui bahwa LSTM with Tuning model yang baik, maka selanjutnya implementasi untuk model tersebut. Berikut hasilnya dalam grafik
   
![image](https://github.com/user-attachments/assets/e00c215c-41ae-4590-a088-b97f91625e9c)

 <p align="justify">
Terlihat bahwa hasil prediksi selama 60 hari kedepan (16 Mei 2025 hingga Juni 2025) memiliki rentang nilai pergerakan IHSG pada harga 6400-6800

## Kesimpulan 
 <p align="justify">
IHSG adalah salah satu indikator pergerakan ekonomi, dengan adanya fenomena turun IHSG pada maret 2025 membuat kemampuan untuk memprediksi IHSG di perlukan. 
   
<p align="justify">
1. Dengan membuat sistem prediksi untuk pergereka IHSG selama 60 hari kedepan, diperlukan data IHSG, Load Data, dan Data Prepration. pada data prepration diperlukan output_size sebanyak 60 dalam teknik windowing, hal ini akan memprediksi 60 hari kedepan
  
 <p align="justify">
2. Diperlukan adanya 2 pemodelan tetapi menggunakan algoritma yang sama yaitu LSTM. 2 pemodelan tersebut LSTM tanpa Tuning dan LSTM dengan Tuning. Kedua Model mengeluarkan hasil evaluasi yang baik, namun LSTM with Tuning jauh lebih baik karena score MAE pada LSTM Tuning jauh lebih rendah. Oleh karena itu untuk pemodelan IHSG dalam pergerakan 60 hari kedepan menggunakan LSTM with Tuning.
   
 <p align="justify"> 
3.  Hasil dari model menunjukkan prediksi IHSG untuk 60 hari ke depan dengan rentang nilai antara 6.400 hingga 6.800. Prediksi ini menunjukkan bahwa model sudah cukup mampu menangkap pola pergerakan IHSG secara umum. Namun, akurasi model masih dapat ditingkatkan agar rentang prediksi lebih mendekati nilai IHSG sebenarnya, sehingga dapat memberikan hasil yang lebih presisi dan andal untuk analisis keuangan di masa depan.

 <p align="justify"> 
   
**Dengan demikian, proses diatas telah menjawab problem statement dan goals. Namun perlu ada perbaikan lebih detail agar nilai prediksi dapat lebih mendekati nilai sebenarnya**

## References 
<p align="justify">
[1] 	DepositoBPR , "DepositoBPR," 07 January 2025. [Online]. Available: https://depositobpr.id/.
    <p align="justify">
[2] 	D. P. Syarina, "ANALISIS PENGARUH NILAI TUKAR RUPIAH, INFLASI DAN INDEKS DOWJONES TERHADAP INDEKS HARGA SAHAM GABUNGAN (IHSG) DI BURSA EFEK INDONESIA (BEI)," vol. 16, no. 3, pp. 542-462. 
    <p align="justify">
[3] 	I. Nofiati, "Hubungan Inflasi, Suku Bunga, Produk Domestik Bruto, Nilai Tukar, Jumlah Uang Beredar, dan Indeks Harga Saham Gabungan (IHSG) Periode 2005–2011," Jurnal Aplikasi Manajemen, vol. 11, no. 2, 2013. 
    <p align="justify">
[4] 	A. K. Hidayat, M. Firdaus and B. Sanim, "PENGARUH KAPITALISASI PASAR SAHAM DAN VARIABEL MAKROEKONOMI TERHADAP INDEKS HARGA SAHAM GABUNGAN DI BURSA EFEK INDONESIA," Jurnal Aplikasi Manajemen dan Bisnis, vol. 5, no. 2, 2019. 











