# Business Understanding
Kemungkinan proses bisnis yang dapat dilakukan pada dataset ini antara lain :
 1. Mensegmentasi pelanggan dari mall yang bersangkutan
 2. Analisis pelanggan dari mall yang bersangkutan
 3. Membuat cluster marketting pada mall yang bersangkutan
# Data Understanding
- Dataset ini memuat data pelanggan dari sebuah mall yang berisikan Jenis Kelamin, Usia, Gaji per Tahun, serta Spending Score yang diberikan oleh mall yang bersangkutan berdasarkan perilaku dan jumlah belanjaan dari pelanggan. Format file dari dataset adalah .csv
- Data yang dimuat berjumlah 200 pelanggan dengan 5 atribut/kolom sebagai berikut :
    - CustomerID : ID yang dimiliki pelanggan.
    - Gender : Jenis kelamin pelanggan.
    - Age : Usia pelanggan.
    - Annual Income : Gaji per tahun dari pelanggan.
    - Spending Score (1-100) : Skor yang diberikan mall untuk pelanggan berdasarkan perilaku dan jumlah belanjaan.
# Data Preparation
- Dataset dipisah menjadi dua bagian menggunakan script [di sini](https://github.com/rmdhnilham/big-data/)
- Setelah terpisah. Salah satu data dimasukkan pada phpmyadmin.
- Sebagai contoh disini dimasukkan data dataset_new1.csv. Convert menjadi sql menggunakan [converter online](https://www.convertcsv.com/csv-to-sql.htm) 
- Sesudah berhasil, import data dataset_new1.sql pada phpmyadmin.
- Hasil data mysql.
