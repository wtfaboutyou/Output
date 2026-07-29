# Memahami Arsitektur Client–Server

## Apa Itu Arsitektur Client–Server?

Arsitektur **Client–Server** adalah model komunikasi dalam jaringan komputer yang membagi tugas menjadi dua peran utama, yaitu **client** sebagai pihak yang meminta layanan atau data, dan **server** sebagai pihak yang menyediakan serta mengelola layanan tersebut.

Sederhananya, client mengirimkan permintaan (**request**) kepada server. Kemudian server menerima permintaan tersebut, memprosesnya, lalu mengirimkan kembali hasilnya (**response**).

Model ini menjadi dasar dari berbagai layanan digital yang digunakan sehari-hari, seperti website, aplikasi mobile, email, media sosial, hingga layanan cloud.

---

## Konsep Dasar Client dan Server

### Client

Client adalah perangkat atau aplikasi yang digunakan pengguna untuk mengakses suatu layanan. Tugas utama client adalah mengirimkan permintaan kepada server dan menampilkan hasil yang diterima.

Contoh client:

- Web browser (Google Chrome, Mozilla Firefox, Microsoft Edge)
- Aplikasi mobile
- Aplikasi desktop
- Komputer atau smartphone

**Contoh:**

Ketika membuka sebuah website menggunakan browser, browser bertindak sebagai **client** yang meminta halaman web kepada server.

---

### Server

Server adalah komputer atau sistem yang menyediakan layanan, data, atau sumber daya kepada client. Server menerima setiap permintaan dari client, memprosesnya, kemudian mengirimkan hasil sesuai kebutuhan.

Contoh server:

- Web Server
- Database Server
- File Server
- Mail Server
- Game Server

Server umumnya memiliki spesifikasi yang lebih tinggi dibandingkan komputer biasa karena harus melayani banyak client secara bersamaan.

---

## Cara Kerja Arsitektur Client–Server

Proses komunikasi antara client dan server berlangsung melalui beberapa tahapan berikut.

1. Client mengirimkan **request** kepada server.
2. Request dikirim melalui jaringan (LAN atau Internet).
3. Server menerima request.
4. Server memproses request sesuai layanan yang diminta.
5. Server mengirimkan **response** kepada client.
6. Client menerima response dan menampilkannya kepada pengguna.

### Ilustrasi Sederhana

```text
+---------+         Request          +---------+
| Client  | -----------------------> |         |
| Browser |                          |  Server |
| Laptop  | <----------------------- |         |
+---------+         Response         +---------+
```

### Contoh pada Website

```text
Pengguna
    │
    ▼
Browser
(Client)
    │
HTTP Request
    │
    ▼
Web Server
    │
Memproses Permintaan
    │
    ▼
Database
    │
Mengirim Data
    ▼
Web Server
    │
HTTP Response
    ▼
Browser Menampilkan Halaman
```

---

## Komponen Arsitektur Client–Server

### 1. Client

Merupakan perangkat atau aplikasi yang digunakan pengguna untuk meminta layanan.

Contoh:

- Browser
- Smartphone
- Laptop

### 2. Server

Menyediakan layanan, menyimpan data, dan memproses permintaan dari client.

Contoh:

- Apache
- Nginx
- Microsoft IIS

### 3. Network (Jaringan)

Media komunikasi yang menghubungkan client dengan server.

Contoh:

- LAN
- Wi-Fi
- Internet

### 4. Protokol

Sekumpulan aturan yang digunakan agar client dan server dapat saling berkomunikasi.

Contoh:

- HTTP
- HTTPS
- FTP
- SMTP
- SSH

---

## Contoh Penerapan

### Mengakses Website

Saat pengguna membuka sebuah website:

1. Browser mengirimkan permintaan ke server.
2. Server mencari data yang diminta.
3. Jika diperlukan, server mengambil data dari database.
4. Server mengirimkan halaman web ke browser.
5. Browser menampilkan halaman kepada pengguna.

### Login ke Media Sosial

Alur kerjanya sebagai berikut.

1. Pengguna memasukkan username dan password.
2. Aplikasi mengirimkan data ke server.
3. Server memeriksa data pada database.
4. Jika data benar, server mengirimkan status berhasil login.
5. Pengguna dapat mengakses akun.

---

## Jenis-Jenis Server

### Web Server

Melayani permintaan halaman web.

Contoh:

- Apache
- Nginx

### Database Server

Menyimpan dan mengelola data aplikasi.

Contoh:

- MySQL
- PostgreSQL
- MariaDB

### File Server

Menyediakan layanan penyimpanan dan berbagi file dalam jaringan.

### Mail Server

Mengelola pengiriman dan penerimaan email.

### Application Server

Menjalankan logika atau proses bisnis suatu aplikasi.

---

## Kelebihan Arsitektur Client–Server

- Data tersimpan secara terpusat.
- Keamanan data lebih mudah dikelola.
- Mudah melakukan pemeliharaan sistem.
- Dapat melayani banyak client secara bersamaan.
- Mudah dikembangkan sesuai kebutuhan.

---

## Kekurangan Arsitektur Client–Server

- Jika server mengalami gangguan, layanan kepada client ikut terganggu.
- Membutuhkan server dengan spesifikasi yang memadai.
- Membutuhkan administrator jaringan.
- Biaya implementasi relatif lebih besar dibandingkan sistem sederhana.

---

## Contoh dalam Kehidupan Sehari-hari

Arsitektur Client–Server digunakan pada berbagai layanan, antara lain:

- Website
- Media sosial
- Email
- Aplikasi mobile
- Game online
- Cloud storage
- Internet banking
- Marketplace

---

## Ringkasan

Arsitektur **Client–Server** adalah model komunikasi jaringan yang membagi peran menjadi dua bagian, yaitu **client** sebagai pihak yang meminta layanan dan **server** sebagai pihak yang menyediakan layanan. Keduanya berkomunikasi melalui mekanisme **request** dan **response** menggunakan jaringan dan protokol tertentu.

Pemahaman terhadap arsitektur Client–Server merupakan dasar penting sebelum mempelajari materi yang lebih lanjut, seperti web server, database server, localhost, port, API, DNS, maupun cloud computing.
