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

### 3. Menampilkan Pelanggan dengan rata-rata transaksi terbesar di Januari 2020
Query SQL berikut ini agar menampilkan 10 pembeli dengan rata-rata nilai transaksi terbesar yang bertransaksi minimal 2 kali di Januari 2020.
```
SELECT 
	buyer_id, 
	count(1) as jumlah_transaksi, 
	avg(total) as avg_nilai_transaksi
FROM 
	orders
WHERE 
	created_at>='2020-01-01' and created_at<'2020-02-01'
GROUP BY 1
having count(1)>= 2
ORDER BY 3 desc
limit 10;
```
keluaran:

![image](https://user-images.githubusercontent.com/62486840/147820925-5e977970-c212-477d-9a2f-646781455aad.png)

### 4. Menampilkan transaksi-transaksi besar di Desember 2019
Query SQL berikut ini akan menampilkan semua nilai transaksi minimal 20,000,000 di bulan Desember 2019, dengan menampilkan nama_pembeli, nilai transaksi dan tanggal_transaksi, urutkan sesuai abjad pembeli
```
SELECT 
	nama_user as nama_pembeli, 
	total as nilai_transaksi, 
	created_at as tanggal_transaksi
FROM 
	orders
inner join 
	users on buyer_id = user_id
WHERE 
	created_at>='2019-12-01' and created_at<'2020-01-01'
and total >= 20000000
OLDER BY 1
```
keluaran:

![image](https://user-images.githubusercontent.com/62486840/147821132-f7e7369e-4722-4b01-be3b-f6b0bc206082.png)

### 5. Menampilkan kategori Produk Terlaris di 2020
Query SQL berikut ini agar menampilkan 5 Kategori dengan total quantity terbanyak di tahun 2020, hanya untuk transaksi yang sudah terkirim ke pembeli. Menampilkan category, total_quantity, total_price.
```
SELECT 
	category, 
	sum(quantity) as total_quantity, 
	sum(price) as total_price
FROM 
	orders
inner join order_details using(order_id)
inner join products using(product_id)
WHERE 
	created_at>='2020-01-01'
	and delivery_at IS NOT NULL
GROUP BY 1
ORDER BY 2 desc
limit 5
```
keluaran:

![image](https://user-images.githubusercontent.com/62486840/147850521-18d36e70-d754-48e5-b3c4-f076fd9684f9.png)

### 6. Mencari pelanggan yang high value
Query SQL untuk mencari pembeli yang sudah bertransaksi lebih dari 5 kali, dan setiap transaksi lebih dari 2,000,000.
Dengan menampilkan nama_pembeli, jumlah_transaksi, total_nilai_transaksi, min_nilai_transaksi kemudian mengurutkan dari total_nilai_transaksi terbesar.
```
SELECT
   nama_user as nama_pembeli,
   count(1) as jumlah_transaksi, 
   sum(total) as total_nilai_transaksi, 
   min(total) as min_nilai_transaksi
FROM
   orders INNER JOIN users ON buyer_id = user_id 
GROUP BY user_id, nama_user
HAVING count(1)> 5 and min(total)> 2000000
ORDER BY 3 DESC;
```
keluaran:

![image](https://user-images.githubusercontent.com/62486840/147850587-bf35b788-ebfb-4a1e-9523-5605589200e3.png)

### 7. Mencari tahu pelanggan yang menjadi dropshipper
Mencari tahu pengguna yang menjadi dropshipper, yakni pembeli yang membeli barang akan tetapi dikirim ke orang lain. Ciri-cirinya yakni transaksinya banyak, dengan alamat yang berbeda-beda.
Dengan SQL query untuk mencari pembeli dengan 10 kali transaksi atau lebih yang alamat pengiriman transaksi selalu berbeda setiap transaksi.
Menampilkan nama_pembeli, jumlah_transaksi, distinct_kodepos, total_nilai_transaksi, avg_nilai_transaksi. Urutkan dari jumlah transaksi terbesar.
```
SELECT
   nama_user as nama_pembeli,
   count(1) as jumlah_transaksi, 
   count(distinct orders.kodepos) as distinct_kodepos,
   sum(total) as total_nilai_transaksi, 
   avg(total) as avg_nilai_transaksi
FROM
   orders INNER JOIN users ON buyer_id = user_id 
GROUP BY user_id, nama_user
HAVING count(1)>= 10 and count(1)=count(distinct orders.kodepos)
ORDER BY 2 DESC;
```
keluaran:

![image](https://user-images.githubusercontent.com/62486840/147850642-f00a4087-7989-4860-8048-9ca5277b7da5.png)

### 8. Mencari reseller Offline atau toko offline
Mencari tahu jenis pengguna yang menjadi reseller offline atau mempunyai toko offline, yakni pembeli yang sering sekali membeli barang dan seringnya dikirimkan ke alamat yang sama. Pembelian juga dengan quantity produk yang banyak. Sehingga kemungkinan barang ini akan dijual lagi.
SQL query berikut ini untuk mencari pembeli yang punya 8 tau lebih transaksi yang alamat pengiriman transaksi sama dengan alamat pengiriman utama, dan rata-rata total quantity per transaksi lebih dari 10.
Dengan menampilkan nama_pembeli, jumlah_transaksi, total_nilai_transaksi, avg_nilai_transaksi, avg_quantity_per_transaksi. Urutkan dari total_nilai_transaksi yang paling besar
```
SELECT
   nama_user as nama_pembeli,
   count(1) as jumlah_transaksi, 
   sum(total) as total_nilai_transaksi, 
   avg(total) as avg_nilai_transaksi,
   avg(total_quantity) as avg_quantity_per_transaksi
FROM
   orders INNER JOIN users ON buyer_id = user_id INNER JOIN (select order_id, 
   sum(quantity) as total_quantity from order_details group by 1) as summary_order using(order_id)
WHERE orders.kodepos=users.kodepos
GROUP BY user_id, nama_user
HAVING count(1)>= 8 AND avg(total_quantity)>10
ORDER BY 3 DESC;
```
keluaran:

![image](https://user-images.githubusercontent.com/62486840/147850742-63bee194-9e83-4bf2-85c6-40f98f6119d8.png)





Project ini dapat diakses melalui https://academy.dqlab.id/main/package/project/261.

![image](https://user-images.githubusercontent.com/62486840/147809285-27d6bf99-763e-47a9-bcd8-189494afcfb9.png)

