## Big Data Genap 2019/2020

**Nama**  : Ramadhan Ilham Irfany<br>
**NRP**   : 05111740000121<br><br><br>

# Analisis Produksi Elektrik

## Business Understanding
Proses yang dilakukan pada dataset yang digunakan adalah :
- Menganalisis rata-rata produksi listrik pada kurun waktu tertentu (time series), dengan mempertimbangkan:
   - **Total Usage**: Keseluruhan Produksi Listrik dalam satuan waktu Bulan (Dikarenakan semua data unik dan tidak memiliki ID). Terdapat variable hari namun hanya 1 variasi
   - **Usage by Year**: Produksi Listrik per Tahun dalam satuan waktu Bulan.
 
## Data Understanding
Data yang digunakan pada proses kali ini adalah Data Produksi Elektrik dengan atribut yang digunakan meliputi:
- **DATE**: Tanggal produksi.
- **IPG2211A2N**: Satuan utilitas elektrik dan gas.

## Data Preparation
Untuk menjalankan workflow ini dapar di ambil dalam repository pada folder ini dan menggunakan dataset yang juga sudah terlampir<br>

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

- Pada node **DB Table Creator**, masukkan konfigurasi seperti berikut, dimana menamai tabel sebagai "meter"<br>
![](Dokumentasi/data-preparation/loaddata-creator.PNG)

- Untuk konfigurasi node **DB Loader** adalah seperti berikut<br>
![](Dokumentasi/data-preparation/loaddata-loader.PNG)

- Setelah itu data sudah masuk pada DB dan siap untuk diproses lebih jauh, cek pada Apache Hive apakah sudah masuk<br>
![](Dokumentasi/modelling/loaddata-hive.PNG)

- Setelah Data berhasil di load, kemundian ubah menjadi data Spark untuk pemodelan dengan node **Hive to Spark**<br>
![](Dokumentasi/modelling/hivetospark.PNG)


## Modelling
![](Dokumentasi/modelling.PNG)

- Pada model ini terdapat 2 metanode yaitu:
	   1. **Extract date-time attributes**: untuk mendapatkan waktu agar dapat di proses untuk selanjutnya
	   2. **Agreagations and time series**: agregasi untuk mencari rata-rata Produksi Listrik per Tahun dalam satuan waktu Bulan
	
 ### 1. Extract date-time attributes
- Untuk metanode Extract date-time attributes bersikan oleh 2 node **Spark SQL Query** sebagai berikut<br>
![](Dokumentasi/extract-date-time-attributes/edta-metanode.PNG)

- Untuk node **Spark SQL Query** pertama yaitu untuk mengkonversikan datetime dari string DATE<br>
![](Dokumentasi/extract-date-time-attributes/edta-convert.PNG)

- Berikut SQL Query yang ada pada node tersebut
```
SELECT 

`DATE`,
`IPG2211A2N` as kw30,
date_add(to_date(from_unixtime(UNIX_TIMESTAMP(`DATE`, 'M/d/yyyy'))), cast(1 as int)) as eventDate

FROM #table# t1
```
Sumber referensi: [String to Date conversion in hive](https://bigdataprogrammers.com/string-date-conversion-hive/)
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
   - **Total Usage**: Keseluruhan Produksi Listrik dalam satuan waktu Bulan (Dikarenakan semua data unik dan tidak memiliki ID). Terdapat variable hari namun hanya 1 variasi
   - **Usage by Year**: Produksi Listrik per Tahun dalam satuan waktu Bulan.
   
- Gambaran prosesnya adalah:
  	- Pengambilan jumlah produksi listrik dengan menggunakan agregasi SUM untuk tiap kategori dengan memakai **month** sebagai parameter GroupBy
	  - Pada kategori **Usage by Year** diambil rata-rata produksi listrik tiap tahun dalam satuan waktu bulan menggunakan agregasi AVG
	  - Semua hasil agregasi diganti nama agar mudah untuk dibaca dan digabungkan menggunakan Joiner 
   
- Sebelum memproses data tambahkan node **Persist Spark DataFream/RDD** dengan konfigurasi sebagai berikut<br>
![](Dokumentasi/aggregation-and-time-series/aats-persisi.PNG)

- Berikut ini adalah dokumentasi untuk tiap-tiap kategori:
#### Total Usage
- Konfigurasi node **Spark GroupBy** untuk pengambilan jumlah produksi listrik dengan menggunakan agregasi SUM degan parameter **month** pada kategori **Total Usage**<br>
![](Dokumentasi/aggregation-and-time-series/aats-total-groupby.PNG)

- Berikut adalah hasil dari agregasi tersebut<br>
![](Dokumentasi/aggregation-and-time-series/aats-total-groupby-result.PNG)

- Konfigurasi node **Spark Column Rename** untuk penggantian nama kolom menjadi **totalKW** agar mudah untuk dibaca<br>
![](Dokumentasi/aggregation-and-time-series/aats-total-rename.PNG)

- Hasil dari pengubahan nama<br>
![](Dokumentasi/aggregation-and-time-series/aats-total-rename-result.PNG)

#### Usage by Year
- Konfigurasi node **Spark GroupBy** untuk pengambilan jumlah produksi listrik dengan menggunakan agregasi SUM degan parameter **month** dan **year** pada kategori **Usage by Year**<br>
![](Dokumentasi/aggregation-and-time-series/aats-year-groupby.PNG)

- Berikut adalah hasil dari agregasi tersebut<br>
![](Dokumentasi/aggregation-and-time-series/aats-total-groupby-result.PNG)

- Konfigurasi node **Spark GroupBy** untuk pengambilan rata-rata produksi listrik tiap tahun dalam satuan waktu bulan menggunakan agregasi AVG<br>
![](Dokumentasi/aggregation-and-time-series/aats-year-groupby-1.PNG)

- Berikut adalah hasil dari agregasi tersebut<br>
![](Dokumentasi/aggregation-and-time-series/aats-total-groupby-1-result.PNG)

- Konfigurasi node **Spark Column Rename** untuk penggantian nama kolom menjadi **avgYearlyKW** agar mudah untuk dibaca<br>
![](Dokumentasi/aggregation-and-time-series/aats-year-rename.PNG)

- Hasil dari pengubahan nama<br>
![](Dokumentasi/aggregation-and-time-series/aats-year-rename-result.PNG)


## Evaluation


## Deployment