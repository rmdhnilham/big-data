# Connect

- Membuka SQLite pada dbeaver 

![](Dokumentasi/connect_to_database.png)

- Mengubah nama semua tabel dengan prefix "05111740000121"

![](Dokumentasi/rename_table.png)

![](Dokumentasi/rename_table-2.png)

![](Dokumentasi/rename_table-3.png)

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


# WritingToDB
