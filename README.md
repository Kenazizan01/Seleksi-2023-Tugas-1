<h1 align="center">
  <br>
  Seleksi Warga Basdat 2023
  <br>
  <br>
</h1>

<h2 align="center">
  <br>
  Data Scraping, Database Modelling & Data Storing Of 100 Best Seller Product in Kokoro Japan Website
  <br>
  <br>
</h2>

## Description of the Data and DBMS
<p>
  Kokoro Japan adalah e-commerce asalh Jepang yang diluncurkan pada tahun 2019. Banyak masyarakat yang sudah mengetahui produk yang berkualitas tinggi dari jepang namun sulit didapatkan pada negara tersebut dan jika ada harganya menjadi mahal .Oleh karena itu, Kokoro didirikan agar masyarakat di seluruh dunia dapat memiliki produk pengalaman berbelanja produk inovasi dan berkualitas tinggi dari negara Jepang dengan harga yang terjangkau. Kategori produk yang dijual diantaranya adalah produk kecantikan seperti skincare dan makeup, produk kesehatan seperti suplemen, dan makanan dan minuman.
</p>
<br>

<p>
  Pada kali ini saya mengambil data dari 100 best seller produt pada Kokoro Japan. Data yang saya scrape dari Kokoro Japan diantaranya adalah nama produk, status produk, harga normal, harga saat sedang sale, rating, dan jumlah review pada produk. Data yang telah diambil akan disimpan pada DBMS PostgreSQL sebagai file SQL. PostgreSQL saya gunakan karena memiliki keamanan yang ketat, memiliki banyak fitur yang baik dalam menyimpan data yang kompleks, prosedur penyimpanan yag baik, dan transaksi dapat dilaksanan sesuai dengan kaidah ACID(Atomicity, Consistency, Isolation,Durability)
</p>
<br>

<p>
  Saya memilih untuk melakukan Scrapping karena saya tertarik untuk melihat tren produk jepang  yang sedang disukai. Beserta untuk mengetahui produk-produk yang memiliki potongan harga yang besar. Serta Produk-produk memiliki rating yang tinggi dengan jumlah review yang banyak
</p>

## Specification of the Program
<p>
  Pada proses data scraping saya menggunakan Jupyter-Lab Pada prosesnya saya menggunakan beberapa library diantaranya adalah BeautifulSoup, Selenium, chromedriver, dan Pandas. Untuk dapat mengakses website Kokoro Japan digunakan webchromedriver. Website kokoro japan adalah website dinamis maka Selenium harus digunakan untuk dapat mengambil seluruh data HTML dari website. Lalu dengan menggunkan beautifulSoup untuk melakukan scrape data yang dibutuhkan. Setelah itu dilakukan parsing,cleansing,dan transforming pada data yang telah didapatkan. Data tersebut dibuat menjadi JSON file dengan bantuan dataframe dari pandas.

  Proses data storing dilakukan dengan membuat perancangan dan pengenmbangan database kokoro japan menggunakan Entity Relation Diagram(ERD). Setelah itu, ERD diagram ditranslasikan menjadi relational diagram untuk dapat diimplementasikan pada DBMS PostgreSQL. Data yang sudah didapatkan dari proses scraping dimasukan ke dalam database. File dump SQL dari database dapat ditemukan pada folder data storing, sub-folder export.
</p>

## How To Use
untuk data scrapping
1. Clone repository ini
2. Install chromedriver melalui link berikut 'https://chromedriver.chromium.org/downloads', pastikan versi chromedriver sesuai versi chrome yang sudah terinstall
3. Masukan chromedriver.exe pada folder data scraping si subfolder src
4. Install semua libray yang digunakan
```
pip install Selenium
pip install bs4
pip install pandas
pip install time
```
5. buka Scraping_Kokoro.ipynb di IDE pilihan anda
6. Run All, jika ada error lakukan Run All lagi

untuk memasukan data SQL ke DBMS PostgreSQL
1. Clone repository
2. Buka command prompt pada folder Data storing, subfolder export
3. Masukan database pada DBMS dengan command berikut
```
psql -U [username] -d[nama_database] < kokoro_japan.sql
```

## JSON FILE STRUCTURE
Berikut hasi struktur hasil scraping dalam format JSON

```
{
          "Product_name":nama dari produk (string),
          "status":status dari produk ('Sale','Not Sale','Sold out'),
          "normal_price":harga normal produk dalam JPY (float),
          "sale_price":harga normal produk dalam JPY (float),
          "ratings":nilai rating dengan nilai maksimal 5 (float),
          "count_reviews":Jumlah review yang didapatkan (integer)
     },
```

contoh isi JSON file
```
{
        "Product_name":"Biore UV Aqua Rich Watery Essence SPF50+\/PA++++ 70ml",
        "status":"Not sale",
        "normal_price":1305.0,
        "sale_price":1305.0,
        "ratings":4.8,
        "count_reviews":76
    },
```

## Database Structure

Berikut merupakan ERD dari database
![erd_design](https://github.com/Kenazizan01/Seleksi-2023-Tugas-1/blob/main/Data%20Storing/design/ERD_kokoro_japan.png)

Berikut merupakan Relational diagram dari database
![relational_diagram]()

## Translasi ERD ke RDBMS
Setiap strong entity pada ERD akan menjadi tabel tersendiri. dan setiap atribut pada entitas akan menjadi kolom pada tabel relasional. Entitas customer menjadi tabel customer, entitas product menjadi tabel product, entitas shopping_cart menjadi tabel shopping_cart, entitas voucher menjadi tabel voucher, dan entitas payment menjadi tabel payment.

1. Pada entitas customer terdapat phone_number yang merupakan multivalue atribut. Oleh karena itu, dibuat tabel customer_phone yang memiliki kolom id_customer dan phone atribut. id_customer menjadi primary key dan sekaligus sebagai foreign key  yang reference pada kolom id_customer pada tabel customer

2. relasi antara customer dengan shopping_cart adalah one-to-many dan many berada pada shopping cart. Sehingga pada tabel shoping cart akan ditambah atribut primary key dari tabel customer yaitu id_customer

3. Relasi antara shopping_cart dengan produk adalah many-to-many sehinga diperlukan tabel baru yang dinamakan fill_up dengan berisikan primary key dari kedua tabel yaitu id_shopping_cart dan id_product. pada relasi tersebut juga terdapat atribut pada relasi, atribut tersebut akan dimasukan ke dalam tabel fill_up.

4. Relasi antara voucher dengan customer adalah mmany-to-one dan kedua bagiannya relasi parsial. Bagian many terdapat pada voucher sehingga pada tabel voucher ditambahkan primary key dari tabel customer yaitu id customer

5. Relasi antara shopping_cart dengan payment adalah one-to-one sehingga dapat dapat memilih tabel yang mana yang akan mendapatkan atribut tambahan yang berupa primary key dari tabel lainnya. Saya memilih untuk menambahkan id_shopping_cart pada tabel payment.

6. Relasi antara shopping cart dengan voucher juga one-to-one hingga dapat dapat memilih tabel yang mana yang akan mendapatkan atribut tambahan yang berupa primary key dari tabel lainnya. Saya memilih untuk menambahkan atribut id_voucher pada tabel payment. Relasi ini juga memiliki atribut sendiri yaitu price-off yang juga ditambahkan pada tabel payment

7. Pada tabel payment terdapat specialization dari payment_method diantaranya terdapat credit_card dan paypal yang memiliki atribut tambahan tersendiri. Oleh karena dibuat tabel untuk lower-level entity masing, tabel itu diisi dengan primary key dari higher-level entity yaitu id_payment dan atribut lokalnya masing-masing.



