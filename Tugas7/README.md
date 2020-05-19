## Big Data Genap 2019/2020

**Nama**  : Ramadhan Ilham Irfany<br>
**NRP**   : 05111740000121<br><br><br>

# Analisis Kebutuhan Listrik di Irlandia

## Business Understanding
Proses yang dilakukan pada dataset yang digunakan adalah :
- Menganalisis rata-rata kebutuhan penggunaan listrik di Irlandia pada kurun waktu tertentu (time series), dengan mempertimbangkan berbagai variabel, yaitu:
   - **Total Usage**: Keseluruhan Penggunaan Listrik.
   - **Usage by Year**: Penggunaan Listrik per Tahun.
   - **Usage by Month**: Penggunaan Listrik per Bulan.
   - **Usage by Week**: Penggunaan Listrik per Minggu.
   - **Usage by Day of Week**: Penggunaan Listrik per Hari dalam Seminggu.
   - **Usage by Day**: Penggunaan Listrik per Hari.
   - **Usage by Day Segment**: Penggunaan Listrik per Hari pada Periode Jam Tertentu.
   - **Usage by Day Classifier**: Penggunaan Listrik pada Pengelompokan Weekend dan Weekday.
   - **Usage by Hour**: Penggunaan Listrik pada Jam Tertentu.


## Data Understanding
Data yang digunakan pada proses kali ini adalah Data Penggunaan Listrik di Irlandia dengan atribut yang digunakan meliputi:
- **meterID**: ID dari meteran listrik.
- **enc_datetime**: Tanggal yang telah dienkripsi.
- **reading**: Nilai meter yang terbaca pada meteran listrik.
 

## Data Preparation
![](Dokumentasi/data-prep.PNG)
- Pertama-tama membuat spark context local menggunakan node **Create Local Big Data Environment**
- Lalu membaca dataset Penggunaan Listrik di Irlandia dengan **File Reader**

- Berikut konfigurasi node **Create Local Big Data Environment**
![](Dokumentasi/data-preparation/createenvirontment-config.PNG)

- Dan berikut untuk konfigurasi node **File Reader**
![](Dokumentasi/data-preparation/filereader-config.PNG)

- Setelah itu menambahkan metanode **Load Data** yang berisikan node-node sebagai berikut
![](Dokumentasi/modelling/loaddata-metanode.PNG)

- Pada node **DB Table Creator**, masukkan konfigurasi seperti berikut, dimana menamai tabel sebagai "meter"
![](Dokumentasi/modelling/loaddata-creator.PNG)

- Untuk konfigurasi node **DB Loader** adalah seperti berikut<br>
![](Dokumentasi/modelling/loaddata-loader.PNG)

- Setelah itu data sudah masuk pada DB dan siap untuk diproses lebih jauh
![](Dokumentasi/modelling/loaddata-result.PNG)

- Untuk optional dapat di cek pada DBeaver apakah DB sudah masuk pada Hive atau belum, pertama dengan mengecek port pada node **Create Local Big Data Environment**
![](Dokumentasi/modelling/loaddata-alamat-hive.PNG)

- Setelah itu masuk ke DBeaver dan membuka Apache Hive dengan URL
```
jdbc:hive2://localhost:56063/
```
- Kemudian lakukan query untuk melihat data pada tabel meter yang sudah dibuat diatas
![](Dokumentasi/modelling/loaddata-hive.PNG)


## Modelling
![](Dokumentasi/model.PNG)
- Setelah Data berhasil di load, kemundian ubah menjadi data Spark dengan node **Hive to Spark** dengan konfigurasi sebagai berikut<br>
![](Dokumentasi/modelling/hivetospark.PNG)

- Pada model ini terdapat 2 metanode yaitu:
	1. **Extract date-time attributes**: untuk mendapatkan waktu agar dapat di proses untuk selanjutnya
	2. **Agreagations and time series**: agregasi penggunaan listrik pada 9 kategori pada **Bussiness Understanding**
	
### 1. Extract date-time attributes
- Untuk metanode Extract date-time attributes bersikan oleh 4 node **Spark SQL Query** sebagai berikut
![](Dokumentasi/extract-date-time-attributes/edta-metanode.PNG)

- Untuk node **Spark SQL Query** pertama yaitu untuk mengkonversikan datetime
![](Dokumentasi/extract-date-time-attributes/edta-spark-query.PNG)

- Berikut SQL Query yang ada pada node tersebut
```
SELECT 

meterid,
enc_datetime,
reading as kw30,
date_add(cast('2008-12-31' as timestamp), cast(substr(enc_datetime, 1, 3) as int)) as eventDate,
concat(
	substr(concat("00", cast(cast((cast(substr(enc_datetime, 4) as int) * 30 / 60) as int) %24 as string)), -2, 2),":", 
	substr(concat("00", cast(cast(cast(substr(enc_datetime, 4) as int) * 30 % 60 as int) as string)), -2, 2)
) as my_time

FROM #table# t1
```
Dimana SQL tersebut akan membuat kolom eventDate yang diambil dari enc_datetime dan my_time yang memiliki format jj:mm

- Berikut hasil dari query tersebut
![](Dokumentasi/extract-date-time-attributes/edta-spark-query-result.PNG)

- Untuk node **Spark SQL Query** kedua yaitu untuk mengekstrak eventDate menjadi jam, hari, minggu, bulan, tahun seperti 9 kategori pada **Bussiness Understanding**
![](Dokumentasi/extract-date-time-attributes/edta-spark-query-1.PNG)

- Berikut SQL Query yang ada pada node tersebut
```
SELECT 

meterid,
kw30,
eventDate,
year(eventDate) as year,
month(eventDate) as month,
weekofyear(eventDate) as week,
date_format(eventDate, 'EEEE') as dayOfWeek,
hour(my_time) as hour

FROM #table# t1
```

- Berikut hasil dari query tersebut
![](Dokumentasi/extract-date-time-attributes/edta-spark-query-result-1.PNG)

- Untuk node **Spark SQL Query** ketiga yaitu untuk mengelompokkan Weekend dan Weekday
![](Dokumentasi/extract-date-time-attributes/edta-spark-query-2.PNG)

- Berikut SQL Query yang ada pada node tersebut
```
SELECT *, 
CASE 
WHEN dayOfWeek in ('Saturday','Sunday') 	THEN 'WE' 
						ELSE 'BD' 
END as dayClassifier

from #table#
```

- Berikut hasil dari query tersebut
![](Dokumentasi/extract-date-time-attributes/edta-spark-query-result-2.PNG)

- Untuk node **Spark SQL Query** terakhir pada metanode ini yaitu untuk membuat segmentasi periode jam pada satu hari
![](Dokumentasi/extract-date-time-attributes/edta-spark-query-3.PNG)

- Berikut SQL Query yang ada pada node tersebut
```
SELECT meterID, kw30, eventDate, year, month, week, dayOfWeek, dayClassifier, hour,
CASE 
WHEN hour >=7 AND hour <9 THEN '7-9'
WHEN hour >=9 AND hour <13 THEN '9-13' 
WHEN hour >=13 AND hour <17 THEN '13-17' 
WHEN hour >=17 AND hour <21 THEN '17-21' 
WHEN hour >=21 OR hour <7 THEN '21-7'  
								 
END as daySegment

from #table#
```

- Berikut hasil dari query tersebut
![](Dokumentasi/extract-date-time-attributes/edta-spark-query-result-3.PNG)


### 2. Agreagations and time series
![](Dokumentasi/aggregation-and-time-series/aats-metanode.PNG)
- Metanode ini adalah untuk membuat time-series berdasarkan 9 kategori pada **Business Understanding** yaitu:
   - **Total Usage**: Keseluruhan Penggunaan Listrik.
   - **Usage by Year**: Penggunaan Listrik per Tahun.
   - **Usage by Month**: Penggunaan Listrik per Bulan.
   - **Usage by Week**: Penggunaan Listrik per Minggu.
   - **Usage by Day of Week**: Penggunaan Listrik per Hari dalam Seminggu.
   - **Usage by Day**: Penggunaan Listrik per Hari.
   - **Usage by Day Segment**: Penggunaan Listrik per Hari pada Periode Jam Tertentu.
   - **Usage by Day Classifier**: Penggunaan Listrik pada Pengelompokan Weekend dan Weekday.
   - **Usage by Hour**: Penggunaan Listrik pada Jam Tertentu.
- Gambaran prosesnya adalah:
	- Pengambilan jumlah penggunaan listrik dengan menggunakan agregasi SUM untuk tiap kategori
	- Lalu pada kategori selain **Total Usage** diambil rata-rata penggunaan listrik tiap kategori menggunakan agregasi AVG
	- Sebagai catatan pada kategori **Usage by Day of Week**, **Usage by Day Segment**, dan **Usage by Day Classifier** perlu dibuatkan pivot untuk mengelompokkan kolom tiap-tiap kluster
	- Setelah itu semua kategori diganti nama agar mudah untuk dibaca dan digabungkan menggunakan Joiner 
- Sebelum memproses data tambahkan node **Persist Spark DataFream/RDD** dengan konfigurasi sebagai berikut
![](Dokumentasi/aggregation-and-time-series/aats-persisi.PNG)

- Berikut ini adalah dokumentasi untuk tiap-tiap kategori:
#### Total Usage
![](Dokumentasi/aggregation-and-time-series/aats-total.PNG)
- Konfigurasi node **Spark GroupBy** untuk pengambilan jumlah penggunaan listrik dengan menggunakan agregasi SUM pada kategori **Total Usage**
![](Dokumentasi/aggregation-and-time-series/aats-total-groupby.PNG)

- Konfigurasi node **Spark Column Rename** untuk penggantian nama kolom menjadi **totalKW** agar mudah untuk dibaca
![](Dokumentasi/aggregation-and-time-series/aggregation-and-time-series/fix.PNG)

#### Usage by Year
![](Dokumentasi/aggregation-and-time-series/aats-year.PNG)
- Konfigurasi node **Spark GroupBy** untuk pengambilan jumlah penggunaan listrik dengan menggunakan agregasi SUM pada kategori **Usage by Year**
![](Dokumentasi/aggregation-and-time-series/aats-year-groupby.PNG)

- Konfigurasi node **Spark GroupBy** untuk pengambilan rata-rata penggunaan listrik pada kategori **Usage by Year** menggunakan agregasi AVG
![](Dokumentasi/aggregation-and-time-series/aats-year-groupby-1.PNG)

- Konfigurasi node **Spark Column Rename** untuk penggantian nama kolom menjadi **avgYearlyKW** agar mudah untuk dibaca
![](Dokumentasi/aggregation-and-time-series/aats-year-rename.PNG)

#### Usage by Month
![](Dokumentasi/aggregation-and-time-series/aats-month.PNG)
- Konfigurasi node **Spark GroupBy** untuk pengambilan jumlah penggunaan listrik dengan menggunakan agregasi SUM pada kategori **Usage by Month**
![](Dokumentasi/aggregation-and-time-series/aats-month-groupby.PNG)

- Konfigurasi node **Spark GroupBy** untuk pengambilan rata-rata penggunaan listrik pada kategori **Usage by Month** menggunakan agregasi AVG
![](Dokumentasi/aggregation-and-time-series/aats-month-groupby-1.PNG)

- Konfigurasi node **Spark Column Rename** untuk penggantian nama kolom menjadi **avgMonthlyKW** agar mudah untuk dibaca<br>
![](Dokumentasi/aggregation-and-time-series/aats-month-rename.PNG)

#### Usage by Week
![](Dokumentasi/aggregation-and-time-series/aats-week.PNG)
- Konfigurasi node **Spark GroupBy** untuk pengambilan jumlah penggunaan listrik dengan menggunakan agregasi SUM pada kategori **Usage by Week**
![](Dokumentasi/aggregation-and-time-series/aats-week-groupby.PNG)

- Konfigurasi node **Spark GroupBy** untuk pengambilan rata-rata penggunaan listrik pada kategori **Usage by Week** menggunakan agregasi AVG
![](Dokumentasi/aggregation-and-time-series/aats-week-groupby-1.PNG)

- Konfigurasi node **Spark Column Rename** untuk penggantian nama kolom menjadi **avgWeeklyKW** agar mudah untuk dibaca
![](Dokumentasi/aggregation-and-time-series/aats-week-rename.PNG)

#### Usage by Day of Week
![](Dokumentasi/aggregation-and-time-series/aats-dow.PNG)
- Konfigurasi node **Spark GroupBy** untuk pengambilan jumlah penggunaan listrik dengan menggunakan agregasi SUM pada kategori **Usage by Day of Week**
![](Dokumentasi/aggregation-and-time-series/aats-dow-groupby.PNG)

- Konfigurasi node **Spark GroupBy** untuk pengambilan rata-rata penggunaan listrik pada kategori **Usage by Day of Week** menggunakan agregasi AVG dan juga mengelompokkan hari pada kolom baru
![](Dokumentasi/aggregation-and-time-series/aats-dow-pivot.PNG)

- Konfigurasi node **Spark Column Rename** untuk penggantian nama kolom menjadi **avg(Nama hari)** agar mudah untuk dibaca
![](Dokumentasi/aggregation-and-time-series/aats-dow-rename.PNG)

#### Usage by Day
![](Dokumentasi/aggregation-and-time-series/aats-day.PNG)
- Konfigurasi node **Spark GroupBy** untuk pengambilan jumlah penggunaan listrik dengan menggunakan agregasi SUM pada kategori **Usage by Day**
![](Dokumentasi/aggregation-and-time-series/aats-day-groupby.PNG)

- Konfigurasi node **Spark GroupBy** untuk pengambilan rata-rata penggunaan listrik pada kategori **Usage by Day** menggunakan agregasi AVG
![](Dokumentasi/aggregation-and-time-series/aats-day-groupby-1.PNG)

- Konfigurasi node **Spark Column Rename** untuk penggantian nama kolom menjadi **avgDaily** agar mudah untuk dibaca
![](Dokumentasi/aggregation-and-time-series/aats-day-rename.PNG)

#### Usage by Day Segment
![](Dokumentasi/aggregation-and-time-series/aats-segment.PNG)
- Konfigurasi node **Spark GroupBy** untuk pengambilan jumlah penggunaan listrik dengan menggunakan agregasi SUM pada kategori **Usage by Day Segment**
![](Dokumentasi/aggregation-and-time-series/aats-segment-groupby.PNG)

- Konfigurasi node **Spark GroupBy** untuk pengambilan rata-rata penggunaan listrik pada kategori **Usage by Day Segment** menggunakan agregasi AVG dan juga mengelompokkan periode jam dalam satu hari pada kolom baru
![](Dokumentasi/aggregation-and-time-series/aats-segment-pivot.PNG)

- Konfigurasi node **Spark Column Rename** untuk penggantian nama kolom menjadi **avg_(segmentasi)** agar mudah untuk dibaca<br>
![](Dokumentasi/aggregation-and-time-series/aats-segment-rename.PNG)

#### Usage by Day Classifier
![](Dokumentasi/aggregation-and-time-series/aats-class.PNG)
- Konfigurasi node **Spark GroupBy** untuk pengambilan jumlah penggunaan listrik dengan menggunakan agregasi SUM pada kategori **Usage by Day Classifier**
![](Dokumentasi/aggregation-and-time-series/aats-class-groupby.PNG)

- Konfigurasi node **Spark GroupBy** untuk pengambilan rata-rata penggunaan listrik pada kategori **Usage by Day Classifier** menggunakan agregasi AVG dan juga mengelompokkan periode jenis hari Weekend atau Weekday pada kolom baru
![](Dokumentasi/aggregation-and-time-series/aats-class-pivot.PNG)

- Konfigurasi node **Spark Column Rename** untuk penggantian nama kolom menjadi **avg_(klasifikasi)** agar mudah untuk dibaca<br>
![](Dokumentasi/aggregation-and-time-series/aats-rename.PNG)

#### Usage by Hour
![](Dokumentasi/aggregation-and-time-series/aats-hour.PNG)
- Konfigurasi node **Spark GroupBy** untuk pengambilan jumlah penggunaan listrik dengan menggunakan agregasi SUM pada kategori **Usage by Hour**
![](Dokumentasi/aggregation-and-time-series/aats-hour-groupby.PNG)

- Konfigurasi node **Spark GroupBy** untuk pengambilan rata-rata penggunaan listrik pada kategori **Usage by Hour** menggunakan agregasi AVG
![](Dokumentasi/aggregation-and-time-series/aats-hour-groupby-1.PNG)

- Konfigurasi node **Spark Column Rename** untuk penggantian nama kolom menjadi **avg_Hourly** agar mudah untuk dibaca
![](Dokumentasi/aggregation-and-time-series/aats-hour-rename.PNG)

- Setelah tiap kategori selesai diproses makan join kan semuanya menggunakan node **Spark Joiner**
![](Dokumentasi/aggregation-and-time-series/aats-joiner.PNG)

- Berikut konfigurasi node **Spark Joiner** menggunakan parameter "meterID"
![](Dokumentasi/aggregation-and-time-series/aats-joiner-config.PNG)


## Evaluation
![](Dokumentasi/eval.PNG)
- Menambahkan node **Spark SQL Query**
- Pada node ini diproses query untuk membuat dan menambahkan kolom reta-rata berisi pesentase penggunaan pada hari di tiap minggu tertentu dan juga periode jam pada hari tertentu
![](Dokumentasi/aggregation-and-time-series/after-aats.PNG)

- Berikut SQL Query yang ada pada node tersebut
```
SELECT `meterID`, `totalKW`, `avgYearlyKW`,`avgMonthlyKW`,`avgWeeklyKW`,
       `avgMonday`,`avgTuesday`,`avgWednesday`,`avgThursday`,`avgFriday`,`avgSaturday`,`avgSunday`,
       `avgDaily`,`avg_7to9`,`avg_9to13`,`avg_13to17`,`avg_17to21`,`avg_21to7`,`avg_BD`,`avg_WE`,`avgHourly`,
       (avgMonday / avgWeeklyKW) * 100.0 as pctMonday,
       (avgTuesday / avgWeeklyKW) * 100.0 as pctTuesday,
       (avgWednesday / avgWeeklyKW) * 100.0 as pctWednesday,
       (avgThursday / avgWeeklyKW) * 100.0 as pctThursday,
       (avgFriday / avgWeeklyKW) * 100.0 as pctFriday,
       (avgSaturday / avgWeeklyKW) * 100.0 as pctSaturday,
       (avgSunday / avgWeeklyKW) * 100.0 as pctSunday,
       (avg_7to9 / avgDaily) * 100.0 as pct_7to9,
       (avg_9to13 / avgDaily) * 100.0 as pct_9to13,
       (avg_13to17 / avgDaily) * 100.0 as pct_13to17,
       (avg_17to21 / avgDaily) * 100.0 as pct_17to21,
       (avg_21to7 / avgDaily) * 100.0 as pct_21to7
       
FROM #table#
```

- Berikut hasil dari keseluruhan kolom yang siap untuk di plootting pada tabel
![](Dokumentasi/aggregation-and-time-series/after-aats-result.PNG)

- Setelah itu menambahkan komponen **PCA, K-means, Scatter Plot** untuk menganalisis menggunakan PCA dan K-means kemudian di plot pada tabel menggunakan Scatter Plot
- Berikut isi dari komponen **PCA, K-means, Scatter Plot**
![](Dokumentasi/pca-kmeans-scatter-plot/scatter-component.PNG)

- Pertama-tama melakukan normalisasi menggunakan node **Spark Normalizer** 
![](Dokumentasi/pca-kmeans-scatter-plot/scatter-component-spark-norm.PNG)

- Kemundian melakukan PCA pada 96% data yang digunakan
![](Dokumentasi/pca-kmeans-scatter-plot/scatter-component-PCA-config.PNG)

- Beginilah hasil dari PCA
![](Dokumentasi/pca-kmeans-scatter-plot/scatter-component-PCA-result.PNG)

- Lalu untuk pengelompokan cluster mengunakan algoritma K-means
![](Dokumentasi/pca-kmeans-scatter-plot/scatter-component-kmeans-config.PNG)

- Dan beginilah hasil dari pengelompokan K-means
![](Dokumentasi/pca-kmeans-scatter-plot/scatter-component-kmeans-result.PNG)

- Tambahkan column filter untuk menyimpan cluster
![](Dokumentasi/pca-kmeans-scatter-plot/scatter-component-columnfilter.PNG)

- Beginilah hasilnya
![](Dokumentasi/pca-kmeans-scatter-plot/scatter-component-columnfilter-result.PNG)

- Setelah itu tambahkan joiner seperti tahap **Agreagations and time series** dengan parameter "meterID" dan beginilah hasilnya<br>
![](Dokumentasi/aggregation-and-time-series/aats-joiner-config.PNG)

- Kemudian tambahkan node **Spark to Table** untuk mengubah data spark menjadi table
- Setelah itu lakukan denormalisasi, dan beginilah hasilnya
![](Dokumentasi/pca-kmeans-scatter-plot/scatter-component-denom-result.PNG)

- Lalu tambahkan node **Number to String** untuk keperluan kustomisasi tabel dengan konfigurasi sebagai berikut
![](Dokumentasi/pca-kmeans-scatter-plot/scatter-component-number-to-string.PNG)

- Beginilah hasil input untuk kustomisasi tabel
![](https://github.com/rmdhnilham/big-data/blob/master/Tugas7/Dokumentasi/pca-kmeans-scatter-plot/scatter-component-transform%20input.PNG)

- Kemudian tambahkan tahapan untuk membuat tabel<br>
![](Dokumentasi/pca-kmeans-scatter-plot/scatter-component-custom-table.PNG)

- Berikut konfigurasi dari node **Color Manager**
![](Dokumentasi/pca-kmeans-scatter-plot/scatter-component-custom-table-color.PNG)

- Berikut konfigurasi dari node **Table View**
![](Dokumentasi/pca-kmeans-scatter-plot/scatter-component-custom-table-view.PNG)

- Berikut konfigurasi dari node **Scatter Plot**
![](Dokumentasi/pca-kmeans-scatter-plot/scatter-component-custom-table-plot.PNG)

- Kemudian pada denormalisasi ubahlah menjadi spark kembali menjadi **Table to Spark**
- Lalu berilah nama menjadi **PCA_dimension_(nomor_dimensi)** menggunakan node **Spark Column Rename**
![](Dokumentasi/pca-kmeans-scatter-plot/scatter-component-rename.PNG)

## Deployment
![](Dokumentasi/deploy.PNG)
- Pada proses deployment di workflow ini terdapat 2 deployment yaitu menggunakan node **Spark to Hive** dan **Spark to Parquet**
- Berikut konfigurasi pada node **Spark to Hive**<br>
![](Dokumentasi/deployment/deployment-hive-config.PNG)

- Dan berikut hasil dari deployment
![](Dokumentasi/deployment/deployment-hive-result.PNG)

- Untuk opsional dapat dilihat pada Hive menggunakan DBeaver
![](Dokumentasi/deployment/deployment-hive-result-db.PNG)

- Berikut konfigurasi pada node **Spark to Parquet**
![](Dokumentasi/deployment/deployment-parquet-config.PNG)

- Dan berikut hasil deploymentnya<br>
![](Dokumentasi/deployment/deployment-parquet-result.PNG)

## Keseluruhan workflow KNIME

![](Dokumentasi/workflow.PNG)
