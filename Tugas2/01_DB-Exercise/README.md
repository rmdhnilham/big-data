# Connect

- Membuka SQLite pada dbeaver 

![](Dokumentasi/connect_to_database.png)

- Mengubah nama semua tabel dengan prefix "05111740000121"

![](Dokumentasi/rename_table.png)

![](Dokumentasi/rename_table-2.png)

![](Dokumentasi/rename_table-4.PNG)

Semua tabel sudah terganti nama

- Execute SQLite Connector

![](Dokumentasi/execute_SQLite_Connector.png)

- Buka node DB Table Selector dan pilih tabel yang diinginkan

![](Dokumentasi/choose_table.png)

- Kemudian execute node tersebut

![](Dokumentasi/DB_Table_Selector-2.PNG)

- Selanjutnya execute node DB Reader dan buka KNIME data table

![](Dokumentasi/DB_Reader.PNG)

# InDB Processing

- Connect SQLite seperti diatas

![](Dokumentasi/2-CONNECT.PNG)

- Select table yang akan diproses

![](Dokumentasi/2-SELECT_TABLE.PNG)

- Tambahkan Column Filter dan remove puma* dan pwgtp* pada tabel

![](Dokumentasi/2-COL_FILTER.PNG)

![](Dokumentasi/2-SETTING_PUMA_PWGTP.PNG)

- Join kan tabel ss13hme dan ss13pme dengan menambahkan DB Joiner dengan acuan Serial No

![](Dokumentasi/2-INNERJOIN_SERIALNO.PNG)

- Setting seperti berikut

![](Dokumentasi/2-INNERJOIN_SERIALNO_SET.PNG)

- Maka hasilnya akan seperti ini

![](Dokumentasi/2-INNERJOIN_SERIALNO_RESULT.PNG)

- Menambahkan Row Filter

![](Dokumentasi/2-ROWFILTER_COWNOTNULL.PNG)

- Filter pada tabel cow yang tidak NULL

![](Dokumentasi/2-ROWFILTER_COWNOTNULL_SET.PNG)

- Maka hasilnya akan seperti ini

![](Dokumentasi/2-ROWFILTER_COWNOTNULL_RES.PNG)

- Tambahkan Row Filter lagi

![](Dokumentasi/2-ROWFILTER_COWNULL.PNG)

- Kali ini filter pada tabel cow yang NULL

![](Dokumentasi/2-ROWFILTER_COWNULL_SET.PNG)

- Maka hasilnya akan seperti ini

![](Dokumentasi/2-ROWFILTER_COWNULL_RES-2.PNG)

- Tambahkan node DB GroupBy

![](Dokumentasi/2-GROUPBY.PNG)

- Setting rata-rata umur dan group by jenis kelamin

![](Dokumentasi/2-GROUPBY_SET.PNG)

- Maka hasilnya akan seperti ini

![](Dokumentasi/2-GROUPBY_RES.PNG)

- Terakhir, sorting umur secara descending dan ambil top 10

![](Dokumentasi/2-SORTBYAGEP.PNG)

- Lakukan setting seperti ini pada sorting

![](Dokumentasi/2-SORTBYAGEP_SET.PNG)

- Lakukan setting seperti ini pada query mengambil 10 teratas

![](Dokumentasi/2-SORTBYAGEP_SET-2.PNG)

- Maka hasilnya akan seperti ini

![](Dokumentasi/2-SORTBYAGEP_RES.PNG)


# Modelling

- Untuk data preparation sama seperti InDB Processing di atas sampai ke Row Filter yang membedakan cow IS NULL atau IS NOT NULL

![](Dokumentasi/3-PREP.PNG)

- Convert Number to String untuk cow IS NOT NULL

![](Dokumentasi/3-CONVERTSTRING_DT.PNG)

- Untuk cow IS NULL tambahkan Column Filter untuk menghilangkan kolom cow yang NULL

![](Dokumentasi/3-REMOVECOLUMN.PNG)

- Masukkan settingan seperti ini

![](Dokumentasi/3-REMOVECOLUMN_SE.PNG)

- Maka hasilnya akan seperti ini

![](Dokumentasi/3-REMOVECOLUMN_RES.PNG)

- Tambahkan Decision Tree Predictor dan sambunkan dari kedua bagian diatas

![](Dokumentasi/3-DTPREDICT.PNG)

- Setting seperti ini

![](Dokumentasi/3-DTPREDICT_SET.PNG)

- Maka hasil tree akan seperti ini 

![](Dokumentasi/3-TREE.PNG)


# WritingToDB

- Simpan file awal sebelum di proses

![](Dokumentasi/4-SAVEORI.PNG)

- Setting seperti berikut

![](Dokumentasi/4-SAVEORI_SET.PNG)

- File awal berasil di back-up

![](Dokumentasi/4-SAVEORI_RES.PNG)

- Simpan model dengan timestamp menggunakan workflow seperti berikut

![](Dokumentasi/4-CREATETIMESTAMP.PNG)

- Settingan PMML To Cell

![](Dokumentasi/4-CREATETIMESTAMP_SET-1.PNG)

- Settingan Create Date&Time Range

![](Dokumentasi/4-CREATETIMESTAMP_SET-2.PNG)

- Settingan Strings to Binary Objects

![](Dokumentasi/4-CREATETIMESTAMP_SET-3.PNG)

- Settingan Joiner

![](Dokumentasi/4-CREATETIMESTAMP_SET-4.PNG)

- Hasil penyimpanan model dan timestamp

![](Dokumentasi/4-CREATETIMESTAMP_RES-2.PNG)

- Tambahkan DB Update untuk mengupdate database setelah proses

![](Dokumentasi/4-DBUPDATE.PNG)

- Setting seperti ini

![](Dokumentasi/4-DBUPDATE_SET.PNG)

- Maka hasilnya akan seperti ini

![](Dokumentasi/4-DBUPDATE_RES.PNG)

- Tambahkan Filter Column untuk mengecek apakah sudah terupdate apa belum

![](Dokumentasi/4-DBUPDATE_FILT.PNG)
