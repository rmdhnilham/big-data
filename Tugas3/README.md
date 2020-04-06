## Big Data Genap 2019/2020

**Nama**  : Ramadhan Ilham Irfany<br>
**NRP**   : 05111740000121<br><br><br>

# Business Understanding
Kemungkinan proses yang dapat dilakukan pada dataset yang digunakan antara lain :
 1. Pengelompokan film berdasarkan genre yang diinginkan
 
 2. Pengelompokan film berdasarkan rating yang diberikan oleh user dari tahun 1995 sampai 2015
 
 3. Merekomendasikan film dalam jumlah tertentu berdasarkan rating yang diberikan oleh user dari tahun 1995 sampai 2015
 
 
# Data Understanding
- Dataset yang digunakan adalah kumpulan data yang menampung rating berbasis 5-bintang berjumlah 20000263 rating, dan 465564 tag dari keseluruhan 27278 film. Data menampung rating dari 138493 user sejak tanggal 9 Januari 1995 sampai 31 Maret 2015. Dataset ini sendiri dibuat pada 17 Oktober 2016.

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

![](Dokumentasi/split.png)
- 
- 

![](Dokumentasi/split-dataset.png)
- 

![](Dokumentasi/write-csv.png)
- 

![](Dokumentasi/save-tables.png)
- 
- 

![](Dokumentasi/save-databases.png)
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
