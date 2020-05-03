## Big Data Genap 2019/2020

**Nama**  : Ramadhan Ilham Irfany<br>
**NRP**   : 05111740000121<br><br><br>

# Membuat Apache Spark Cluster Menggunakan Docker


## Membuat Apache Spark Cluster
- Pertama membuat script bernama `docker-compose.yml` yang berisi:
```
version: '2'

services:
  spark:
    image: bitnami/spark:2
    environment:
      - SPARK_MODE=master
      - SPARK_RPC_AUTHENTICATION_ENABLED=no
      - SPARK_RPC_ENCRYPTION_ENABLED=no
      - SPARK_LOCAL_STORAGE_ENCRYPTION_ENABLED=no
      - SPARK_SSL_ENABLED=no
    ports:
      - '8080:8080'
  spark-worker-1:
    image: bitnami/spark:2
    environment:
      - SPARK_MODE=worker
      - SPARK_MASTER_URL=spark://spark:7077
      - SPARK_WORKER_MEMORY=1G
      - SPARK_WORKER_CORES=1
      - SPARK_RPC_AUTHENTICATION_ENABLED=no
      - SPARK_RPC_ENCRYPTION_ENABLED=no
      - SPARK_LOCAL_STORAGE_ENCRYPTION_ENABLED=no
      - SPARK_SSL_ENABLED=no
  spark-worker-2:
    image: bitnami/spark:2
    environment:
      - SPARK_MODE=worker
      - SPARK_MASTER_URL=spark://spark:7077
      - SPARK_WORKER_MEMORY=1G
      - SPARK_WORKER_CORES=1
      - SPARK_RPC_AUTHENTICATION_ENABLED=no
      - SPARK_RPC_ENCRYPTION_ENABLED=no
      - SPARK_LOCAL_STORAGE_ENCRYPTION_ENABLED=no
      - SPARK_SSL_ENABLED=no
```
- Script diatas membuat 1 spark master dan 2 spark worker dengan 2 core
- Kemudian jalankan perintah berikut pada command prompt:
```
docker-compose up
```
![](Dokumentasi/up.PNG)

- Tunggu sampai proses pembuatan selesai
![](Dokumentasi/done.PNG)

- Setelah itu cek apakah container berhasil dibuat
![](Dokumentasi/cek.PNG)

- Tes spark cluster melalui [http://localhost:8080](http://localhost:8080) dan akan muncul tampilan sebagai berikut
![](Dokumentasi/UI.PNG)


## Menjalankan Script Python di Dalam Apache Spark Cluster
- Perhatikan CONTAINER ID dari spark master kemudian lakukan perintah bash seperti berikut:
```
docker exec -it <container_id> /bin/bash
```
![](Dokumentasi/bash-1.PNG)

- Cek alamat IP spark master menggunakan
```
hostname -i
```
![](Dokumentasi/hostname.PNG)

- Kemudian menggunakan fitur dari Apache Spark yaitu `spark-submit`. dalam tugas ini akan dilakukan penghitungan nilai Pi.
- Jalankan perintah berikut:
```
spark-submit --master spark://172.19.0.4:7077 examples/src/main/python/pi.py 100
```
- 172.19.0.4 diatas adalah alamat IP dari Apache Spark pada port 7077
- Konstanta 100 merupakan partisi dari proses penghitungan Pi yang akan dijalankan
- Cek kembali web [http://localhost:8080](http://localhost:8080) untuk memonitoring proses
- Apabila proses sedang berlangsung akan muncul tampilan seperti berikut:
![](Dokumentasi/used-1.PNG)

- Tunggu sampai perintah selesai dijalankan
![](Dokumentasi/jobdone.PNG)


## Perbandingan
- Dalam tugas ini akan dilakukan perbandingan pada spesifikasi berikut:
    - Jumlah worker: 2, 5
    - Jumlah CPU: 2, 4
    - Memory: 1G
    - Partisi: 100, 1000
    
- Hasil perbadingan menggunakan 2 worker dengan 2 CPU antara 100 dan 1000 partisi
![](Dokumentasi/compare.PNG)

- Hasil perbadingan menggunakan 5 worker dengan 4 CPU antara 100 dan 1000 partisi
![](Dokumentasi/compare-1.PNG)

- Hasil perbadingan menggunakan 2 worker dengan 4 CPU antara 100 dan 1000 partisi
![](Dokumentasi/compare-2.PNG)

- Hasil perbadingan menggunakan 5 worker dengan 2 CPU antara 100 dan 1000 partisi
![](Dokumentasi/compare-3.PNG)


## Kesimpulan
