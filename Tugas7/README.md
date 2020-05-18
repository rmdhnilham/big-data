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


## Modelling


## Evaluation


## Deployment


SQL:
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

```
SELECT *, 
CASE 
WHEN dayOfWeek in ('Saturday','Sunday') 	THEN 'WE' 
									        ELSE 'BD' 
END as dayClassifier

from #table#
```

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
