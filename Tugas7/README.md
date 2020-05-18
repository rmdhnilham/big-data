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
![](Dokumentasi/data-preparation/dataprep.PNG)
- Pertama-tama membuat spark context local menggunakan node **Create Local Big Data Environment**
- Lalu membaca dataset Penggunaan Listrik di Irlandia dengan **File Reader**

- Berikut konfigurasi node **Create Local Big Data Environment**
![](Dokumentasi/data-preparation/createenvirontment-config.PNG)

- Dan berikut untuk konfigurasi node **File Reader**
![](Dokumentasi/data-preparation/filereader-config.PNG)


## Modelling
![](Dokumentasi/modelling/modelling.PNG)
- Pertama dengan menambahkan metanode **Load Data** yang berisikan node-node sebagai berikut
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

- Setelah Data berhasil di load, kemundian ubah menjadi data Spark dengan node **Hive to Spark** dengan konfigurasi sebagai berikut
![](Dokumentasi/modelling/hivetospark.PNG)


## Evaluation
![](Dokumentasi/extract-date-time-attributes/evaluation.PNG)
- Dalam proses evaluasi ini terbagi menjadi 2 metanode, 1 node, dan 1 komponen. Antara lain:
1. Metanode **Extract date-time attributes**: untuk mendapatkan waktu agar dapat di proses untuk selanjutnya
2. Metanode **Agreagations and time series**: agregasi penggunaan listrik pada 9 kategori pada **Bussiness Understanding**
3. Node **Spark SQL Query**: query untuk menghitung persentase dari penggunaan listrik secara per hari, dan pada hari saat periode jam tertentu
4. Komponen **PCA, K-means, Scatter Plot**: untuk menganalisis menggunakan PCA dan K-means kemudian di plot pada tabel menggunakan Scatter Plot

###1. Extract date-time attributes
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


###2. Agreagations and time series


###3. Spark SQL Query


###4. PCA, K-means, Scatter Plot


## Deployment


SQL:

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
