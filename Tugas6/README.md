## Big Data Genap 2019/2020

**Nama**  : Ramadhan Ilham Irfany<br>
**NRP**   : 05111740000121<br><br><br>

List workflow :
- [MLlib model to PMML](https://github.com/rmdhnilham/big-data/tree/master/Tugas6#mllib-model-to-pmml)
- [Spark Compiled Model Predictor](https://github.com/rmdhnilham/big-data/tree/master/Tugas6#spark-compiled-model-predictor)

# MLlib model to PMML

## Business Understanding

Kemungkinan proses yang dapat dilakukan pada dataset yang digunakan antara lain :

1. Mengklasifikasi spesies bunga iris

2. Percobaan untuk data mining

3. Percobaan untuk menggunakan machine learning


## Data Understanding

![](Dokumentasi/dataset.PNG)

Data yang digunakan pada proses kali ini adalah [Iris Data Set](https://archive.ics.uci.edu/ml/datasets/Iris) dimana dataset ini  merupakan dataset yang paling terkenal dari Ronald Aylmer Fisher yang sering digunakan dalam literasi seputar pattern recognition. Dataset ini berisi tentang bunga iris yang memiliki 3 class dan 50 jenis pada setiap class dimana 1 class terpisah secara linear dari lainnya.
Adapun atribut yang digunakan meliputi:
- sepal length in cm
- sepal width in cm
- petal length in cm
- petal width in cm
- class:
   - Iris Setosa
   - Iris Versicolour
   - Iris Virginica 


## Data Preparation

![](Dokumentasi/dataprep.PNG)
- Pertama-tama membuat spark context local menggunakan node **Create Local Big Data Environment**
- Lalu membaca dataset Iris dengan **File Reader**
- Kemudian menambahkan node **Table to Spark** untuk menaruh data table ke dataframe spark
<br>

![](Dokumentasi/create-config.PNG)
- Konfigurasi pada node **Create Local Big Data Environment**
<br>

![](Dokumentasi/reader-config.PNG)
- Konfigurasi pada node **File Reader**
<br>

![](Dokumentasi/spark-config.PNG)
- Konfigurasi pada node **Table to Spark**
<br>


## Modelling

![](Dokumentasi/model.PNG)
- Menambahkan node **Spark k-Means** untuk train model ke dalam spark
- Lalu menambahkan node **Spark MLlib to PMML** untuk mengubah model di dalam spark menjadi PMML
- Kemudian menambahkan node **PMML Compiler** untuk menerjemahkan model PMML ke Java yang nantinya akan dijalankan oleh node **Compiled Model Predictor
<br>

![](Dokumentasi/kmeans-config.PNG)
- Konfigurasi pada node **Spark k-Means**
<br>

![](Dokumentasi/mllib-config.PNG)
- Konfigurasi pada node **Spark MLlib to PMML**
<br>

![](Dokumentasi/compiler-config.PNG)
- Konfigurasi pada node **PMML Compiler**
<br>


## Evaluation

![](Dokumentasi/eval.PNG)
- Untuk evaluasi, pertama-tama membaca dataset Iris dengan menambahkan node **File Reader**
- Lalu menambahkan node **Compiled Model Predictor** untuk membuat prediction
- Kemudian menambahkan node **Entropy Scorer** untuk melakukan penghitungan entropi dari prediction yang telah dibuat
<br>

![](Dokumentasi/reader1-config.PNG)
- Konfigurasi pada node **File Reader**
<br>

![](Dokumentasi/predictor-config.PNG)
- Konfigurasi pada node **Compiled Model Predictor**
<br>

![](Dokumentasi/scorer-config.PNG)
- Konfigurasi pada node **Entropy Scorer**
<br>

![](Dokumentasi/scorer-result.PNG)
- Hasil dari penghitungan entropy
<br>


## Deployment

![](Dokumentasi/deploy.PNG)
- Untuk deployment, pertama-tama memasukkan inputan JSON dengan menambahkan node **Container Input (JSON)**
- Lalu menambahkan node **JSON to Table** untuk mengubah input JSON menjadi tabel multi kolom
- Kemudian menambahkan node **Compiled Model Predictor** untuk membuat prediction
- Setelah itu menambahkan node **Table to JSON** untuk mengubah kembali tabel multi kolom ke bentuk JSON
- Terakhir, menambahkan node **Container Output (JSON)** untuk memperlihatkan output JSON
<br>

![](Dokumentasi/container-config.PNG)
- Konfigurasi pada node **Container Input (JSON)**
<br>

![](Dokumentasi/json-config.PNG)
- Konfigurasi pada node **JSON to Table**
<br>

![](Dokumentasi/predictor-config.PNG)
- Konfigurasi pada node **Compiled Model Predictor**
<br>

![](Dokumentasi/table-config.PNG)
- Konfigurasi pada node **Table to JSON**
<br>

![](Dokumentasi/output-config.PNG)
- Konfigurasi pada node **Container Output (JSON)**
<br>

![](Dokumentasi/scorer-result.PNG)
- Hasil dari output JSON dimana terdapat sebuah cluster yang terbuat untuk pengklasifikasian bunga Iris
<br>

## Keseluruhan workflow KNIME

![](Dokumentasi/knime.PNG)

# Spark Compiled Model Predictor

## Business Understanding

 
## Data Understanding


## Data Preparation


## Modelling


## Evaluation


## Deployment
