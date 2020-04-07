## Big Data Genap 2019/2020

**Nama**  : Ramadhan Ilham Irfany<br>
**NRP**   : 05111740000121<br><br><br>

# Business Understanding
Kemungkinan proses yang dapat dilakukan pada dataset yang digunakan antara lain :
 1. Pengelompokan film berdasarkan genre yang diinginkan
 
 2. Pengelompokan film berdasarkan rating yang diberikan oleh user dari tahun 1995 sampai 2015
 
 3. Merekomendasikan film dalam jumlah tertentu berdasarkan rating yang diberikan oleh user dari tahun 1995 sampai 2015
 
 
# Data Understanding
- Dataset yang digunakan bersumber dari [MovieLens](http://movielens.org). 

- Dataset yang disajikan memuat rating berbasis 5-bintang berjumlah 20000263 rating, dan 465564 tag dari keseluruhan 27278 film. Data menampung rating dari 138493 user sejak tanggal 9 Januari 1995 sampai 31 Maret 2015. Dataset ini sendiri dibuat pada 17 Oktober 2016.

- Data yang dimuat dalam [MovieLens 20M Dataset](https://grouplens.org/datasets/movielens/) ini terdapat beberaba file CSV, dimana setiap CSV memiliki data atribut dengan rincian sebagai berikut:
    - genome-scores.csv = berisikan data relevansi antara suatu film dan tag nya untuk kepentingan rekomendasi
       - movieId
       - tagId
       - relevance
    - genome-tags.csv = berisikan data pengidentifikasian tag yang diberikan oleh user untuk kepentingan rekomendasi
       - tagId
       - tag
    - links.csv = berisikan data tentang sumber rating (imdb dan tmdb)
       - movieId
       - imdbId
       - tmdbId
    - movies.csv = berisikan daftar film yang tersedia
       - movieId
       - title
       - genres
    - ratings.csv = berisikan data rating yang diberikan oleh user
       - userId
       - movieId
       - rating
       - timestamp
    - tags.csv = berisikan tag yang diberikan oleh user
       - userId
       - movieId
       - tag
       - timestamp


# Data Preparation

![](Dokumentasi/create-environment.PNG)
- Pertama-tama membuat spark context local menggunakan Create Local Big Data Environment

![](Dokumentasi/create-environment-config.PNG)
- Setting pada Create Local Big Data Environment

![](Dokumentasi/build-user-profile.PNG)
- Lalu membuat profil user dengan ID 9999 untuk memberi rating pada 20 film acak

![](Dokumentasi/file-reader-config.PNG)
- Konfigurasi pada node File Reader untuk membaca file movies.csv

![](Dokumentasi/add-fields-component.PNG)
- Di dalam node add fields terdapat beberapa komponen
- Node add fields sendiri merupakan penambahan kolom untuk user ID 9999

![](Dokumentasi/row-splitter-config.PNG)
- Konfigurasi pada node Row Splitter untuk mengambil 20 film

![](Dokumentasi/ask-ratings.PNG)
- Di dalam node Ask User for Movie Ratings terdapat beberapa komponen
- Node Ask User for Movie Ratings ini meminta user ID 9999 untuk merating 20 movie yang terpilih

![](Dokumentasi/ask-ratings-value.PNG)
- Konfigurasi untuk memberi rating di dalam komponen Constant Value Column
- Disini saya memberi rating 5 yang menandakan 20 film tersebut sangat disukai oleh user ID 9999 dan akan mencari rekomendasi dari film tersebut

![](Dokumentasi/ratings.PNG)
- Film sudah diberikan rating

![](Dokumentasi/no-ratings.PNG)
- Di dalam node no rating terdapat beberapa komponen
- Node no rating sendiri berfungsi untuk membuat list film dengan rating 0

![](Dokumentasi/table-to-spark.PNG)
- Kemudian tambahkan node Table to Spark dan pisahkan film dengan rating ke proses testing dan tanpa rating ke deployment

![](Dokumentasi/csv-to-spark.PNG)
- Setelah selesai dengan rating dari user, sambungkan pada Big Data Environment yang telah dibuat sebelumnya
- Lalu tambahkan node CSV to Spark untuk membaca data ratings.csv dari MovieLens untuk proses testing

![](Dokumentasi/csv-to-spark-config.PNG)
- Konfigurasi pada node CSV to Spark yang berfungsi untuk membaca data ratings.csv

![](Dokumentasi/csv-to-spark-partitioning.PNG)
- Konfigurasi node Spark Partitioning yang berfungsi untuk mempartisi data rating.csv menjadi 80% untuk proses Training dan 20% sisanya untuk proses Testing

# Modelling

![](Dokumentasi/modelling.png)
### 
- 

![](Dokumentasi/read-csv.png)
### 
- 

![](Dokumentasi/read-DB.png)
### 
- 

![](Dokumentasi/append.png)
# Evaluation
- 

# Deployment
- 

![](Dokumentasi/deploy.png)
### Hasil Deploy CSV

![](Dokumentasi/save-dataset-CSV.png)
### Hasil Deploy Database

![](Dokumentasi/save-dataset-DB.png)
