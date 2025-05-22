# Predictive-Analysis-IHSG-TimeSeries

## A. Domain Proyek
Analisis dan Prediksi IHSG Menggunakan Pendekatan Time Series
### Latar Belakang
<p align="justify"> 
  IHSG atau Indeks Harga Saham Gabungan adalah sebuah indeks yang menampilkan rata-rata pergerakan seluruh saham di Bursa Efek Indonesia (BEI). Indeks tersebut memiliki fungsi dapat sebagai pergerakan saham di pasar modal, lalu menganalisis portfolio jika investor ingin menanamkan modal di Indonesia, dan salah satu tolok ukur melihat pertumbuhan ekonomi. Dengan adanya IHSG sebagai salah satu tolok ukur pertumbuhan ekonomi, pada bulan Maret 2025 IHSG mengalami penurunan, masyarakat Indonesia serentak membicarakan hal tersebut dan mengklaim “Indonesia tidak baik-baik saja”.
<p align="justify"> 
  Adanya IHSG sebagai indikator salah satu pertumbuhan ekonomi didukung oleh beberapa penelitian dimana Nilai tukar rupiah, inflasi, suku bunga, harga minyak dunia berpengaruh terhadap naik turunya IHSG. Kemampuan dalam memprediksi pergerakan IHSG merupakan hal penting  untuk mengambil keputusan yang tepat. Salah satu yang digunakan untuk memprediksi IHSG adalah time series analysis. 
<p align="justify"> 
  Dengan demikian, proyek ini bertujuan untuk membuat model yang dapat memprediksi IHSG yang memanfaatkan data historis sehingga dapat memberikan gambaran awal mengenai pergerakan indeks IHSG.

## B. Business Understanding
### 1. Problem Statement
- Bagaimana membuat model machine learning yang dapat memprediksi IHSG di masa depan?
- Bagaimana teknik model yang paling baik untuk membuat model machine learning?
- Apa insight menarik dari analisis IHSG?

### 2. Goals
- Membuat Model Machine learning untuk memprediksi IHSG di masa depan 
- Membuat teknik model yang paling baik untuk membuat model machine learning
- Mencari insight menarik dari analisis IHSG

### 3. Solution Statements
- Data IHSG dimuat lalu dibersihkan untuk mengetahui pola dalam data
- Data IHSG diproses menjadi time series dan dibagi dengan data training 80% dan validasi 20%
- Data diubah menjadi format window untuk mempelajari hgubungan antar waktu secara berurutan
- Dilakukan dua pendekatan dalam pengembangan model yaitu LSTM tanpa Tuning dan LSTM dengan Tuning
- Model dievaluasi dengan MAE dan kurva loss
- Hasil prediksi dalam bentuk grafik

## C. Data Understanding
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
|Change | %	Persentase perubahan harga IHSG dibandingkan dengan hari sebelumnya. Contoh: 0.13% berarti harga naik 0.13% dibanding hari sebelumnya|

**2. Mempunyai baris berjumlah 8500**

**3. Semua variabel bertipe data object**

**4. Mempunyai missing value pada variable Vol.**

**5. Tidak mempunyai duplikasi data**

## D. Cleaning Data
Setelah memahami data, maka data perlu dibersihkan. Oleh karena itu, dibuat fungsi cleaning dengan berbeda-beda kegunaan

|Fungsi_Cleaning|Deskripsi|
|----|----|
|delete_missing_value(df)|Fungsi untuk menghapus missing value|
|delete_columns(df, columns_to_delete)| Fungsi untuk menghapus kolom|
|convert_to_datetime(df, col_name)|Fungsi untuk mengubah tipe data menjadi datetime|
|convert_and_sort_datetime(df, col_name)|Fungsi untuk mengurutkan tanggal secara ascending|
|convert_price_to_float(df, col_name)|Fungsi untuk mengubah tipe data menjadi float|
|convert_percent_to_float(df, col_name)|Fungsi untuk mengubah tipe data menjadi float jika nilai unik pada data memiliki simbol %|

<p align="justify">
Setelah membuat fungsi untuk cleaning data, maka apply fungsi tersebut. Pertama menghapus missing value, lalu menghapus kolom Open, High, Low, dan Vol karena tidak sesuai dengan tujuan analisa dan mengembangkan sistem prediksi. Lalu mengubah tipe data Date menjadi datetime dan mengurutkannya. Setelah itu mengubah tipe data Price yang sebelumnya object menjadi float, dan mengubah nilai pada kolom Change % menjadi bentuk float. Setelah dibersihkan data IHSG mempunyai 3 variabel/kolom dan 7816 baris.

## D. EDA - mengetahui pola/trend/insight 
**1. Mengetahui outiers**
<p align="center"><img src="https://github.com/ejapten/Predictive-Analysis-IHSG-TimeSeries/blob/main/assets/EDA%20-%20Outliers.png" style="display: block; margin: auto;" width="400"/></p>
<p align="justify">
Dalam variabel  Price tidak ada outliers, namun pada variable Change % Terdapat outliers. Tidak masalah untuk Change karena variabel ini tidak akan diikutsertakan dalam model prediksi 

**2. Distribusi Data**
<p align="center"><img src="https://github.com/ejapten/Predictive-Analysis-IHSG-TimeSeries/blob/main/assets/EDA-%20Distribusi%20Data.png" style="display: block; margin: auto;" width="400"/></p>
Variable Price berdistribusi tidak normal dan Change % normal

**3. Deskripsi Statistik Data**
<p align="center"><img src="https://github.com/ejapten/Predictive-Analysis-IHSG-TimeSeries/blob/main/assets/EDA%20-%20Statistik.png" style="display: block; margin: auto;" width="400"/></p>
<p align="justify">
Rentang tanggal pada data IHSG adalah 4 Januari 1993 hingga 15 Mei 2025 dimana dalam rentang tersebut rata-rata IHSG sekitar 3.023,57. Dengan rata-rata sekitar hal itu, membuat nila minimum adalah 256,83 dan maksimum 7.905,39. Selain itu, Standar Deviasi sebesar 0.014 menunjukan perubahan harian yang cukup besar. 
Dengan adanya deskripsi data menunjukan Perubahan harian juga sangat bervariasi, dengan beberapa hari mengalami perubahan besar baik positif maupun negatif, mencerminkan dinamika pasar saham Indonesia yang dipengaruhi berbagai faktor ekonomi dan politik


**4. Pola Harga IHSG**
<p align="justify">
Dengan menggunakan data pertama yaitu 1993 hingga terakhir pada mei 2025 maka tahun 2024 menjadi tahun yang dengan harga IHSG paling tinggi, sedangkan tahun yang memiliki harga IHSG paling rendah berada pada tahun 1998. 
<p align="center"><img src="https://github.com/ejapten/Predictive-Analysis-IHSG-TimeSeries/blob/main/assets/EDA%20-%20Harga.png" style="display: block; margin: auto;" width="800"/></p>
<p align="justify">
Berdasarkan visualisasi diatas dapat terlihat jelas bahwa IHSG terjadi penurunan pada rentang 1996-2000 dimana kemungkinan besar adalah pada 1998. Lalu terjadi penurunan lagi pada rentang 2008-2012 secara tajam.Penurunan secara tajam lagi terjadi pada 2020 hingga kemungkinan 2022. Lalu pada 2025 terjadi penurunan secara tajam lagi pada bulan maret
<p align="justify">
Selain penurunan, pada 2000 terjadi kenaikan IHSG, lalu pada periode 2011 kemungkinan besar juga mengalami kenaikkan. Lalu rentang 2016-2020 terjadi kenaikan harga yang paling tinggi di antara tahun sebelumnya. Sebelum IHSG merosot turun pada maret 2025 IHSG pernah mengalami kenaikan.

<p align="center"><img src="https://github.com/ejapten/Predictive-Analysis-IHSG-TimeSeries/blob/main/assets/EDA%20-%20vol.png" style="display: block; margin: auto;" width="800"/></p>
<p align="justify">
Berdasarkan persentase perubahan harga IHSG, terjadi naik turun setiap hari nya, jika dilihat periode 1996-2000 terjadi persentase kenaikan dan penurunan yang paling tajam dari tahun lainnya.

## E. Data Preparation 
Tahap data preparation analisis ini untuk mempersiapkan data pada proses modelling, dengan melakukan beberapa tahapan

**1. Normalisasi Data**

<p align="justify">
Pada proyek ini, hanya 2 variabel yang akan digunakan untuk mencapai tujuan yaitu variabel Date dan Price. Tetapi, hanya variabel price yang akan dinormalisasikan menggunakan Min-Max Scaling agar nilai pada price dengan rentang 0 - 1.

**2.. Mengubah Price dan Date menjadi Array**

Setelah dinormalisasi, data variabel price dan date dimuat dalam array untuk Splitting data dan bisa dimuat dalam model nanti.

**3. Memuat data train dan data validation**

<p align="justify">
Selanjutnya membagi dataset untuk proses latih dan uji model. train_series berisi 80% data untuk melatih model, sedangkan val_series berisi 20% data untuk menguji performa model pada data yang belum dilatih

**4. Membuat windowing**

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

## F. Modeling 
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

## G. Evaluasi
<p align="justify">
Setelah model selesai dilatih, langkah berikutnya adalah evaluasi performa model untuk mengetahui seberapa baik prediksi yang dihasilkan.

**1. LSTM without Tuning**
<p align="center"><img src="https://github.com/ejapten/Predictive-Analysis-IHSG-TimeSeries/blob/main/assets/Evaluation%20-%20LSTM%20without%20Tunning.png" style="display: block; margin: auto;" width="400"/></p>

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
<p align="center"><img src="https://github.com/ejapten/Predictive-Analysis-IHSG-TimeSeries/blob/main/assets/Evaluation%20-%20LSTM%20with%20Tunning.png" style="display: block; margin: auto;" width="400"/></p>

  **Analisis Grafik:**
    - Train dan validation loss sama sama turun dengan cepaat dan kemudian mendatar di angka kecil
    
    - Tidak ada gap besar
    
    - Validation loss sedikit lebih tinggi dari training loss, tapi polanya mengikuti dan stabil, tidak naik tajam di akhir epoch.

  **Analisis Score:**
    - nilai  loss dan mae pada training dan validasi sama sama kecil
    
    - mae validasi sedikit lebih tinggi dari training tapi gap nya tidak besar
    
    - Tidak ada tanda overfitting (yaitu training loss jauh lebih kecil dari validation loss).
    
    - Validation MAE juga masih cukup rendah.

  **Kesimpulan:**
  
    - Model LSTM Tidak Overfitting dan Underfitting



**4. Compare**
<p align="center"><img src="https://github.com/ejapten/Predictive-Analysis-IHSG-TimeSeries/blob/main/assets/Evaluation%20-%20Compare.png" style="display: block; margin: auto;" width="400"/></p>
 <p align="justify">
Model LSTM with Tunning memiliki MAE yang lebih rendah dari LSTM without tuning baik dari train dan validation. maka LSTM with Tunning model yang paling baik untuk prediksi

## H. Result
 <p align="justify">
Setelah mengetahui bahwa LSTM with Tuning model yang baik, maka selanjutnya implementasi untuk model tersebut. Berikut hasilnya dalam grafik
<p align="center"><img src="https://github.com/ejapten/Predictive-Analysis-IHSG-TimeSeries/blob/main/assets/Result.png" style="display: block; margin: auto;" width="800"/></p>
 <p align="justify">
Terlihat bahwa hasil prediksi selama 60 hari kedepan (16 Mei 2025 hingga Juni 2025) memiliki rentang nilai pergerakan IHSG pada harga 6400-6800

## References 
 <p align="justify">
Apa itu IHSG? Ini Pengertian, Fungsi, & Cara Mencari Datanya. (2025, January 7). bunga deposito bpr. Retrieved May 21, 2025, from https://depositobpr.id/blog/ihsg-adalah
    <p align="justify">
Hidayat, A. K., Firdaus, M., & Sanim, B. (2019, Mei). PENGARUH KAPITALISASI PASAR SAHAM DAN VARIABEL MAKROEKONOMI TERHADAP INDEKS HARGA SAHAM GABUNGAN DI BURSA EFEK INDONESIA. Jurnal Aplikasi Manajemen dan Bisnis, 5(2). http://dx.doi.org/10.17358/jabm.5.2.332
       <p align="justify">
Nofiatin, I. (2013, Juni). Hubungan Inflasi, Suku Bunga, Produk Domestik Bruto, Nilai Tukar, Jumlah Uang Beredar, dan Indeks Harga Saham Gabungan (IHSG) Periode 2005–201. Jurnal Aplikasi Manajemen, 11(2).
          <p align="justify">
Pakar ITS: Merosotnya IHSG Berdampak Signifikan pada Perekonomian Indonesia. (2025, March 21). Institut Teknologi Sepuluh Nopember (ITS). Retrieved May 21, 2025, from https://www.its.ac.id/news/2025/03/21/pakar-its-merosotnya-ihsg-berdampak-signifikan-pada-perekonomian-indonesia/
             <p align="justify">
Syarina, D. P. (n.d.). ANALISIS PENGARUH NILAI TUKAR RUPIAH, INFLASI DAN INDEKS DOW JONES TERHADAP INDEKS HARGA SAHAM GABUNGAN (IHSG) DI BURSA EFEK INDONESIA (BEI). 6(3), 542 - 562.










