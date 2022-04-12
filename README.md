# DQLab_Project-Data-Analysis-for-B2B-Retail_-Customer-Analytics-Report.md
This is my practice project from DQLab

![89724555-b77d1480-da2e-11ea-995f-22d7ded46ab3](https://user-images.githubusercontent.com/64195148/162917205-26885370-bb7b-4106-92ef-f514ca4eeb65.png)

Latar Belakang

xyz.com adalah perusahan rintisan B2B yang menjual berbagai produk tidak langsung kepada end user tetapi ke bisnis/perusahaan lainnya. Sebagai data-driven company, maka setiap pengambilan keputusan di xyz.com selalu berdasarkan data. Setiap quarter xyz.com akan mengadakan townhall dimana seluruh atau perwakilan divisi akan berkumpul untuk me-review performance perusahaan selama quarter terakhir.
Tugas dan Langkah

Sebagai seorang data analyst, kamu dimintai untuk menyediakan data dan analisa mengenai kondisi perusahaan bulan terakhir untuk dipresentasikan di townhall tersebut. (Asumsikan tahun yang sedang berjalan adalah tahun 2004).

Adapun hal yang akan direview adalah :

    Bagaimana pertumbuhan penjualan saat ini?
    Apakah jumlah customers xyz.com semakin bertambah ?
    Dan seberapa banyak customers tersebut yang sudah melakukan transaksi?
    Category produk apa saja yang paling banyak dibeli oleh customers?
    Seberapa banyak customers yang tetap aktif bertransaksi?

Langkah yang akan dilakukan :

    Menggunakan klausa “Select … From …” untuk mengambil data di database
    Menggunakan klausa Where dan Operator untuk menfilter data
    Menggunakan “group by”dan fungsi aggregat untuk aggregasi penjualan dan revenue
    Menggunakan “order by” untuk mengurutkan data
    Menggunakan “union” untuk menggabungkan tabel data penjualan
    Menggunakan “date and time function” dan fungsi text untuk data manipulation
    Menggunakan subquery untuk menyimpan hasil sementara untuk digunakan kembali dalam query.

![1](https://user-images.githubusercontent.com/64195148/162918613-8bad867f-8aee-4809-b9da-39d6698c4c62.png)

1. Memahami table

![2](https://user-images.githubusercontent.com/64195148/162918926-00c550be-0929-4e72-bfc8-71aea1606994.png) 
![3](https://user-images.githubusercontent.com/64195148/162918942-1192ede9-c492-4453-bad5-cd7709e51ec3.png)

Query sqlnya :
- SELECT * FROM orders_1 limit 5;
- SELECT * FROM orders_2 limit 5;
- SELECT * FROM customer limit 5;


2. Total Penjualan dan Revenue pada Quarter-1 (Jan, Feb, Mar) dan Quarter-2 (Apr,Mei,Jun)
![4](https://user-images.githubusercontent.com/64195148/162919879-fa26204d-880c-49d2-8c03-f98779dc894f.png)

Query sqlnya :
- select sum(quantity) as total_penjualan,sum(quantity*priceeach) as revenue from orders_1;
- select sum(quantity) as total_penjualan,sum(quantity*priceeach) as revenue from orders_2 where status = 'Shipped';

3. Menghitung persentasi keseluruhan penjualan

![5](https://user-images.githubusercontent.com/64195148/162920184-ddeb0594-ea2e-4b3e-ad4d-fb47d595a4e4.png)

Query sqlnya :
- select quarter,sum(quantity) as total_penjualan,sum(quantity*priceeach) as revenue from 
(select ordernumber,status,quantity,priceeach, 1 as quarter from orders_1
UNION
select ordernumber,status,quantity,priceeach, 2 as quarter from orders_2)
as tabel_a where status = 'Shipped'
group by quarter;

![6](https://user-images.githubusercontent.com/64195148/162920337-bf11735a-c530-4ee0-b3dc-c92e0d4f0dcc.png)

4. Apakah jumlah customers xyz.com semakin bertambah?

![7](https://user-images.githubusercontent.com/64195148/162921377-b60e3f5b-bbc1-4a55-9173-370791605eec.png)

Query sqlnya :
- select quarter(createdate) as quarter, count(distinct customerid) as total_customers from 
(select customerid,createdate,quarter(createdate) as quarter from customer where createdate between '2004-01-01' and '2004-06-30') as tabel_b
group by quarter(createdate);

5. Seberapa banyak customers tersebut yang sudah melakukan transaksi?

![8](https://user-images.githubusercontent.com/64195148/162921698-3156a708-c174-4cfd-b41a-8649ee4ef583.png)

![9](https://user-images.githubusercontent.com/64195148/162921708-eb0e2881-6b28-4181-8f37-f4c427c9d5c2.png)

Query sqlnya :
- select quarter(createdate) as quarter, count(distinct customerid) as total_customers from 
(select customerid,createdate,quarter(createdate) as quarter from customer where createdate between '2004-01-01' and '2004-06-30' and customerid in 
 (select distinct customerid from orders_1
UNION
select distinct customerid from orders_2)) as tabel_b
group by quarter(createdate);

6. Category produk apa saja yang paling banyak di-order oleh customers di Quarter-2?

![10](https://user-images.githubusercontent.com/64195148/162921926-231f3b7b-41bc-4c2a-b9f2-41475ad9e177.png)
![11](https://user-images.githubusercontent.com/64195148/162921936-5c3ca1e1-6a97-437d-8286-24f495956dfe.png)

Query sqlnya :
- select left(productcode,3) as categoryid,count(distinct ordernumber) as total_order,sum(quantity) as total_penjualan from 
(select productcode,ordernumber,quantity,status,left(productcode,3) as categoryid from orders_2 where status = "Shipped") tabel_c
group by left(productcode,3)
order by count(distinct ordernumber)desc;

7. Seberapa banyak customers yang tetap aktif bertransaksi setelah transaksi pertamanya?

![12](https://user-images.githubusercontent.com/64195148/162922428-deb858ed-370d-408f-b0c0-020bad015b6e.png)
![13](https://user-images.githubusercontent.com/64195148/162922447-56e89f64-0b57-4a8f-9b0f-8ff834255f8f.png)

Query sqlnya :
- #Menghitung total unik customers yang transaksi di quarter_1
SELECT COUNT(DISTINCT customerID) as total_customers FROM orders_1;
#output = 25
SELECT "1" as quarter, (COUNT(DISTINCT customerID)*100)/25 as q2 FROM orders_1 where customerid in (SELECT DISTINCT customerID FROM orders_2);

Kesimpulan

Berdasarkan data yang telah kita peroleh melalui query SQL, Kita dapat menarik kesimpulan bahwa :
1. Performance xyz.com menurun signifikan di quarter ke-2, terlihat dari nilai penjualan dan revenue yang drop hingga 20% dan 24%,
2. perolehan customer baru juga tidak terlalu baik, dan sedikit menurun dibandingkan quarter sebelumnya.
3. Ketertarikan customer baru untuk berbelanja di xyz.com masih kurang, hanya sekitar 56% saja yang sudah bertransaksi. 
Disarankan tim Produk untuk perlu mempelajari behaviour customer dan melakukan product improvement, sehingga conversion rate 
(register to transaction) dapat meningkat.
4. Produk kategori S18 dan S24 berkontribusi sekitar 50% dari total order dan 60% dari total penjualan, sehingga xyz.com sebaiknya fokus 
untuk pengembangan category S18 dan S24.
5. Retention rate customer xyz.com juga sangat rendah yaitu hanya 24%, artinya banyak customer yang sudah bertransaksi di quarter-1 
tidak kembali melakukan order di quarter ke-2 (no repeat order).
6. com mengalami pertumbuhan negatif di quarter ke-2 dan perlu melakukan banyak improvement baik itu di sisi produk dan bisnis marketing, 
jika ingin mencapai target dan positif growth di quarter ke-3. Rendahnya retention rate dan conversion rate bisa menjadi diagnosa awal bahwa customer tidak tertarik/kurang puas/kecewa berbelanja di xyz.com.
