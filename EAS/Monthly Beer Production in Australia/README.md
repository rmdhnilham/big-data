## Big Data Genap 2019/2020

**Nama**  : Ramadhan Ilham Irfany<br>
**NRP**   : 05111740000121<br><br><br>

# Analisis Produksi Beer Bulanan di Australia

## Business Understanding
Proses yang dilakukan pada dataset yang digunakan adalah :
- Menganalisis rata-rata produksi beer di Australia pada kurun waktu tertentu (time series), dengan mempertimbangkan:
   - **Total Usage**: Keseluruhan Produksi Beer dalam satuan waktu Bulan (Dikarenakan semua data unik dan data disajikan dalam format bulanan).
   - **Usage by Year**: Produksi Beer per Tahun dalam satuan waktu Bulan.
 
## Data Understanding
Data yang digunakan pada proses kali ini adalah Data Produksi Beer Bulanan di Australia dengan atribut yang digunakan meliputi:
- **Month**: Bulan produksi.
- **Monthly beer production**: Rata-rata produksi beer bulanan.

## Data Preparation
Untuk menjalankan workflow ini dapat di ambil dalam repository pada folder ini dan menggunakan dataset yang juga sudah terlampir<br>

![](Dokumentasi/dataprep.PNG)

- Pertama-tama membuat spark context local menggunakan node **Create Local Big Data Environment**
- Lalu membaca dataset Penggunaan Listrik di Irlandia dengan **File Reader**

- Berikut konfigurasi node **Create Local Big Data Environment**<br>
![](Dokumentasi/data-preparation/create-config.PNG)

- Dan berikut untuk konfigurasi node **File Reader**<br>
![](Dokumentasi/data-preparation/filereader-config.PNG)

pastikan membuka file dataset (.csv) yang terlampir

- Setelah itu menambahkan metanode **Load Data** yang berisikan node-node sebagai berikut<br>
![](Dokumentasi/data-preparation/loaddata-metanode.PNG)

- Pada node **DB Table Creator**, masukkan konfigurasi seperti berikut, dimana menamai tabel sebagai "beer"<br>
![](Dokumentasi/data-preparation/loaddata-creator.PNG)

- Untuk konfigurasi node **DB Loader** adalah seperti berikut<br>
![](Dokumentasi/data-preparation/loaddata-loader.PNG)

- Setelah itu data sudah masuk pada DB dan siap untuk diproses lebih jauh, cek pada Apache Hive apakah sudah masuk<br>
![](Dokumentasi/data-preparation/loaddata-hive.PNG)

- Setelah Data berhasil di load, kemundian ubah menjadi data Spark untuk pemodelan dengan node **Hive to Spark**<br>
![](Dokumentasi/data-preparation/hivetospark.PNG)


## Modelling
![](Dokumentasi/modelling.PNG)

- Pada model ini terdapat 2 metanode yaitu:
	1. **Extract date-time attributes**: untuk mendapatkan waktu agar dapat di proses untuk selanjutnya
	2. **Agreagations and time series**: agregasi untuk mencari rata-rata Produksi Beer per Tahun dalam satuan waktu Bulan
	
 ### 1. Extract date-time attributes
- Untuk metanode Extract date-time attributes bersikan oleh 2 node **Spark SQL Query** sebagai berikut<br>
![](Dokumentasi/extract-date-time-attributes/edta-metanode.PNG)

- Untuk node **Spark SQL Query** pertama yaitu untuk mengkonversikan datetime dari string Month<br>
![](Dokumentasi/extract-date-time-attributes/revisi-convert.PNG)

- Berikut SQL Query yang ada pada node tersebut
```
SELECT 

`Month`,
`Monthly beer production` as kw30,
date_add(to_date(from_unixtime(UNIX_TIMESTAMP(`Month`, 'yyyy-MM'))), cast(16 as int)) as eventDate

FROM #table# t1
```
Sumber referensi: [String to Date conversion in hive](https://bigdataprogrammers.com/string-date-conversion-hive/). Saya menambahkan int 16 untuk mengeset tanggal karena Hive support untuk format yyyy-MM-dd
- Berikut hasil dari query tersebut<br>
![](Dokumentasi/extract-date-time-attributes/edta-convert-result.PNG)

- Untuk node **Spark SQL Query** kedua yaitu untuk mengekstrak/mengambil tahun dan bulan pada datetime yang sudah dibuat<br>
![](Dokumentasi/extract-date-time-attributes/edta-extract.PNG)

- Berikut SQL Query yang ada pada node tersebut
```
SELECT 

`kw30`,
`eventDate`,
year(eventDate) as year,
month(eventDate) as month

FROM #table# t1
```
- Berikut hasil dari query tersebut<br>
![](Dokumentasi/extract-date-time-attributes/edta-extract-result.PNG)

### 2. Agreagations and time series
![](Dokumentasi/aggregation-and-time-series/aats-metanode.PNG)

- Metanode ini adalah untuk membuat time-series berdasarkan 2 kategori pada **Business Understanding** yaitu:
   - **Total Usage**: Keseluruhan Produksi Beer dalam satuan waktu Bulan (Dikarenakan semua data unik dan data disajikan dalam format bulanan).
   - **Usage by Year**: Produksi Beer per Tahun dalam satuan waktu Bulan.
   
- Gambaran prosesnya adalah:
	- Pengambilan jumlah produksi beer dengan menggunakan agregasi SUM untuk tiap kategori dengan memakai **month** sebagai parameter GroupBy
	- Pada kategori **Usage by Year** diambil rata-rata produksi beer tiap tahun dalam satuan waktu bulan menggunakan agregasi AVG
	- Semua hasil agregasi diganti nama agar mudah untuk dibaca dan digabungkan menggunakan Joiner 
   
- Sebelum memproses data tambahkan node **Persist Spark DataFream/RDD** dengan konfigurasi sebagai berikut<br>
![](Dokumentasi/aggregation-and-time-series/aats-persisi.PNG)

- Berikut ini adalah dokumentasi untuk tiap-tiap kategori:
#### Total Usage
- Konfigurasi node **Spark GroupBy** untuk pengambilan jumlah produksi beer dengan menggunakan agregasi SUM degan parameter **month** pada kategori **Total Usage**<br>
![](Dokumentasi/aggregation-and-time-series/aats-total-groupby.PNG)

- Berikut adalah hasil dari agregasi tersebut<br>
![](Dokumentasi/aggregation-and-time-series/aats-total-groupby-result.PNG)

- Konfigurasi node **Spark Column Rename** untuk penggantian nama kolom menjadi **totalKW** agar mudah untuk dibaca<br>
![](Dokumentasi/aggregation-and-time-series/aats-total-rename.PNG)

- Hasil dari pengubahan nama<br>
![](Dokumentasi/aggregation-and-time-series/aats-total-rename-result.PNG)

#### Usage by Year
- Konfigurasi node **Spark GroupBy** untuk pengambilan jumlah produksi beer dengan menggunakan agregasi SUM degan parameter **month** dan **year** pada kategori **Usage by Year**<br>
![](Dokumentasi/aggregation-and-time-series/aats-year-groupby.PNG)

- Berikut adalah hasil dari agregasi tersebut<br>
![](Dokumentasi/aggregation-and-time-series/aats-year-groupby-result.PNG)

- Konfigurasi node **Spark GroupBy** untuk pengambilan rata-rata produksi beer tiap tahun dalam satuan waktu bulan menggunakan agregasi AVG<br>
![](Dokumentasi/aggregation-and-time-series/aats-year-groupby-1.PNG)

- Berikut adalah hasil dari agregasi tersebut<br>
![](Dokumentasi/aggregation-and-time-series/aats-total-groupby-1-result.PNG)

- Konfigurasi node **Spark Column Rename** untuk penggantian nama kolom menjadi **avgYearlyKW** agar mudah untuk dibaca<br>
![](Dokumentasi/aggregation-and-time-series/aats-year-rename.PNG)

- Hasil dari pengubahan nama<br>
![](Dokumentasi/aggregation-and-time-series/aats-year-rename-result.PNG)

- Setelah selesai memproses 2 kategori, buatlah node **Spark Joiner** untuk menggabungkan kategori tersebut untuk proses selanjutnya<br>
![](Dokumentasi/aggregation-and-time-series/aats-join.PNG)

- Dan inilah data yang akan digunakan untuk tahap evaluasi<br>
![](Dokumentasi/aggregation-and-time-series/aats-join-result.PNG)


## Evaluation
![](Dokumentasi/eval.PNG)

- Pada tahap Evaluasi menggunakan komponen-komponen seperti **PCA, K-means, Scatter Plot** untuk menganalisis menggunakan PCA dan K-means kemudian di plot pada tabel menggunakan Scatter Plot
- Berikut isi dari komponen **PCA, K-means, Scatter Plot**<br>
![](Dokumentasi/pca-kmeans-scatter-plot/pca-mertanode.PNG)

- Pertama-tama melakukan normalisasi menggunakan node **Spark Normalizer**<br>
![](Dokumentasi/pca-kmeans-scatter-plot/pca-normalizer.PNG)

- Kemundian melakukan PCA (**Principal Component Analysis**). Dikarenakan dataset berukukan kecil maka saya menggunakan 100% dari dataset<br>
![](Dokumentasi/pca-kmeans-scatter-plot/pca-pca.PNG)

- Beginilah hasil dari PCA yang telah dilakukan<br>
![](Dokumentasi/pca-kmeans-scatter-plot/pca-pca-result.PNG)

- Lalu untuk pengelompokan cluster mengunakan algoritma K-means dengan node **Spark k-Means**<br>
![](Dokumentasi/pca-kmeans-scatter-plot/pca-kmeans.PNG)

- Dan beginilah hasil dari pengelompokan K-means<br>
![](Dokumentasi/pca-kmeans-scatter-plot/pca-kmeans-result.PNG)

- Tambahkan column filter untuk menampilkan month dan cluster<br>
![](Dokumentasi/pca-kmeans-scatter-plot/pca-columnfilter.PNG)

- Beginilah hasilnya setelah di filter<br>
![](Dokumentasi/pca-kmeans-scatter-plot/pca-columnfilter-result.PNG)

- Setelah itu tambahkan joiner seperti dengan parameter "month"<br>
![](Dokumentasi/pca-kmeans-scatter-plot/pca-join.PNG)

- Dan beginilah hasil setelah menggabungkan **PCA** dan **K-Means**<br>
![](Dokumentasi/pca-kmeans-scatter-plot/pca-join-result.PNG)

- Kemudian tambahkan node **Spark to Table** untuk mengubah data spark menjadi table
- Setelah itu lakukan denormalisasi untuk membuat table view
- Lalu tambahkan node **Number to String** untuk keperluan kustomisasi tabel dengan konfigurasi sebagai berikut<br>
![](Dokumentasi/pca-kmeans-scatter-plot/pca-number.PNG)

- Beginilah hasil input untuk kustomisasi tabel<br>
![](Dokumentasi/pca-kmeans-scatter-plot/pca-number-result.PNG)

- Lalu atur warna pada cluster menggunakan node **Color Manager**<br>
![](Dokumentasi/pca-kmeans-scatter-plot/pca-color.PNG)

- Beginilah hasil dari pewarnaan cluster dalam bentuk tabel<br>
![](Dokumentasi/pca-kmeans-scatter-plot/pca-color-result.PNG)

- Kemudian lakukan plotting menggunakan node **Scatter Plot**<br>
![](Dokumentasi/pca-kmeans-scatter-plot/pca-plot.PNG)

- Beginilah hasil dari plotting menggunakan **Scatter Plot**<br>
![](Dokumentasi/pca-kmeans-scatter-plot/pca-plot-result.PNG)

- Setelah itu membuat node **Table View** untuk membuat tabel Aggregated Beer Data<br>
![](Dokumentasi/pca-kmeans-scatter-plot/revisi.PNG)

- Dan beginilah tampilan JavaScript untuk tabel<br>
![](Dokumentasi/pca-kmeans-scatter-plot/pca-revisi-view-beer-result.PNG)

- Kemudian pada denormalisasi ubahlah menjadi spark kembali menggunakan **Table to Spark**
- Menghilangkan underscore pada PCA dimension untuk deployment menggunakan node **Spark Column Rename**<br>
![](Dokumentasi/pca-kmeans-scatter-plot/pca-rename.PNG)


## Deployment
![](Dokumentasi/deploy.PNG)

- Pada proses deployment di workflow ini terdapat 2 deployment yaitu menggunakan node **Spark to Hive** dan **Spark to Parquet**
- Berikut konfigurasi pada node **Spark to Hive**<br>
![](Dokumentasi/deployment/deploy-hive.PNG)

dikarenakan saya mengerjakan 4 dataset simultan maka saya akan mengatur **Remove table** jika table sudah ada sebelumnya
- Dan berikut hasil dari deployment<br>
![](Dokumentasi/deployment/deploy-hive-result.PNG)

- Berikut konfigurasi pada node **Spark to Parquet**<br>
![](Dokumentasi/deployment/deploy-parquet.PNG)

untuk parquet-nya sama seperti hive dikarenakan saya mengerjakan simultan maka saya rename **Target name** sesuai dataset
- Dan berikut hasil deploymentnya<br>
![](Dokumentasi/deployment/deploy-parquet-result.PNG)
