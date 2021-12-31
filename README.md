# Data-Analysis-for-E-Commerce-Challenge
Project dari DQLab tentang Tantangan Data Analysis pada sebuah E-Commerce menggunakan SQL

Dataset yang digunakan merupakan data dari DQLab Store yang merupakan e-commerce dimana pembeli dan penjual saling bertemu. Pengguna bisa membeli barang dari pengguna lain yang berjualan. Setiap pengguna bisa menjadi pembeli sekaligus penjual.

Ada 4 tabel yang tersedia,

* users, berisi detail data pengguna. Berisi,
    + user_id : ID pengguna
    + nama_user : nama pengguna
    + kodepos : kodepos alamat utama dari pengguna
    + email : email dari pengguna
* products, berisi detail data dari produk yang dijual. Berisi,
    + product_id : ID produk
    + desc_product : nama produk
    + category : kategori produk
    + base_price : harga asli dari produk
* orders, berisi transaksi pembelian dari pembeli ke penjual. Berisi,
    + order_id : ID transaksi
    + seller_id : ID dari pengguna yang menjual
    + buyer_id : ID dari pengguna yang membeli
    + kodepos : kodepos alamat pengirimian transaksi (bisa beda dengan alamat utama)
    + subtotal : total harga barang sebelum diskon
    + discount : diskon dari transaksi
    + total : total harga barang setelah dikurangi diskon, yang dibayarkan pembeli
    + created_at : tanggal transaksi
    + paid_at : tanggal dibayar
    + delivery_at : tanggal pengiriman
* order_details, berisi detail barang yang dibeli saat transaksi. Berisi,
    + order_detail_id : ID table ini
    + order_id : ID dari transaksi
    + product_id : ID dari masing-masing produk transaksi
    + price : harga barang masing-masing produk
    + quantity : jumlah barang yang dibeli dari masing-masing produk
    
Dalam Project ini tugas yang ingin kerjakan:
1. Mencari 10 transaksi terbesar dari seorang pelanggan.
2. Menampilkan summary transaksi per bulan di tahun 2020.
3. Menampilkan Pelanggan dengan rata-rata transaksi terbesar di Januari 2020.
4. Menampilkan transaksi-transaksi besar di Desember 2019.
5. Menampilkan kategori Produk Terlaris di 2020.
6. Mencari pelanggan yang high value.
7. Mencari tahu pelanggan yang menjadi dropshipper.
8. Mencari reseller Offline atau toko offline.
9. Mencari pembeli sekaligus penjual.
10. Mengetahui bagaimana trend lama waktu transaksi dibayar sejak dibuat.


### 1. 10 Transaksi terbesar dari seorang pelanggan
Query SQL berikut ini agar menampilkan 10 transaksi dari pembelian dari pengguna dengan user_id 12476, urutkan dari nilai transaksi paling besar. 
```
SELECT 
	seller_id,
	buyer_id, 
	total as nilai_transaksi, 
	created_at as tanggal_transaksi
FROM 
	orders
WHERE 
	buyer_id = 12476
ORDER BY 3 desc
LIMIT 10
```
keluaran:

![image](https://user-images.githubusercontent.com/62486840/147819431-7216d482-b016-4e91-ae62-e25956fdd761.png)

### 2. Menampilkan summary transaksi per bulan di tahun 2020
Query SQL berikut ini akan menampilkan summary transaksi per bulan di tahun 2020.
```
SELECT 
	EXTRACT(YEAR_MONTH FROM created_at) as tahun_bulan, 
	count(1) as jumlah_transaksi, 
	sum(total) as total_nilai_transaksi
FROM 
	orders
WHERE 
	created_at>='2020-01-01'
GROUP BY 1
ORDER BY 1
```
keluaran:

![image](https://user-images.githubusercontent.com/62486840/147819646-5dacb558-1512-416f-9d59-b4a9c72bd327.png)










Project ini dapat diakses melalui https://academy.dqlab.id/main/package/project/261.

![image](https://user-images.githubusercontent.com/62486840/147809285-27d6bf99-763e-47a9-bcd8-189494afcfb9.png)

