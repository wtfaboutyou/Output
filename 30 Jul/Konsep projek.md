# Arsitektur Dropmes (Anonymous Messages)
```
                [Pengunjung Anonim]              [Pemilik Akun]
                        |                               |
                 isi form pesan                 login Telegram
                        |                               |
                        v                               v
          ------> [Cloudflare: DNS, Proxy, TLS, Turnstile Captcha] <------
                                    |
                                    v
                    ------------------------------------
                    |         Server Debian 13         |
                    |   Nginx (reverse proxy + SSL)    |
                    |                                  |
                    |   [Next.js App] --> [PostgreSQL] |
                    |         |                        |
                    |         v                        |
                    |    [Telegram Bot: notifikasi]    |
                    |                                  |
                    |    [Prometheus] --> [Grafana]    |
                    ------------------------------------

```
# Tech Stack

| Layer | Pilihan | Alasan |
|-------|----------|---------------------|
| Frontend + Backend | Next.js | Mempercepat waktu pengembangan karena tampilan dan server logic dibangun dalam satu sistem yang sama, sehingga biaya development lebih efisien dan website lebih cepat dirilis. Next.js akan berisi kode untuk mengirim dan menerima voice note, Ia akan memakai BullMQ untuk mengelola tugas-tugas "di balik layar" seperti mengonversi audio. Next.js akan memanggil FFmpeg (sebuah alat konversi media) untuk mengubah format voice note agar lebih ringan. |
| Autentikasi | Telegram Login Widget | Login sekali tap tanpa akun/password baru, sekaligus otomatis jadi kanal notifikasi karena kita sudah terhubung ke Telegram user. |
| Database | PostgreSQL | Relasi data (user, link, pesan) jelas, jadi tetap cepat & akurat diakses walau jumlah pesan terus bertambah serta mampu mendukung pertumbuhan data pengguna dalam jangka panjang. PgBouncer akan menjadi "resepsionis" khusus yang mengelola koneksi ke PostgreSQL, memastikan database tidak kewalahan jika banyak pengguna mengakses bersamaan. |
| Cache/Antrean Tugas | Redis | Redis akan menjadi "buku catatan" super cepat untuk mengatur antrean tugas BullMQ (konversi voice note), mencatat sementara jumlah kunjungan link sebelum disimpan permanen ke database secara berkala, dan membatasi jumlah pesan yang bisa dikirim dari satu pengguna dalam waktu singkat (mencegah spam). |
| Penyimpanan file besar | Cloudflare R2 | Cloudflare R2 akan menjadi penyimpanan khusus untuk semua file voice note. Ini terpisah dari database utama dan didesain untuk menyimpan file berukuran besar secara efisien. |
| Notifikasi | Telegram Bot API | Memberikan notifikasi instan kepada pengguna tanpa memerlukan infrastruktur notifikasi berbayar seperti SMS gateway atau layanan push notification. |
| Reverse Proxy + Keamanan Akses | Nginx + Let's Encrypt | Menjamin koneksi HTTPS yang aman bagi seluruh pengguna tanpa biaya sertifikat SSL tahunan. |
| Containerization | Docker | Mempermudah proses deployment, pembaruan aplikasi, dan pemulihan sistem saat terjadi gangguan sehingga downtime dapat diminimalkan. |
| Anti-spam / Proteksi Bot | Cloudflare Turnstile + Rate Limiting | Melindungi web dari spam dan bot yang dapat mengganggu layanan tanpa memerlukan biaya berlangganan. |
| CDN / DNS / Keamanan | Cloudflare (Free Plan) | Mempercepat akses website dari berbagai lokasi sekaligus memberikan perlindungan terhadap berbagai serangan siber tanpa biaya bulanan. |
| Monitoring Sistem | Prometheus + Grafana | Memantau kondisi server secara real-time sehingga potensi masalah dapat dideteksi lebih awal tanpa biaya lisensi perangkat lunak monitoring komersial. |
| Pemantauan Ketersediaan Layanan | Uptime Kuma | Memberikan notifikasi otomatis ketika layanan mengalami gangguan sehingga kestabilan sistem tetap terjaga. |
| Otomatisasi Rilis (CI/CD) | GitHub Actions | Mengotomatisasi proses build dan deployment sehingga proses rilis lebih cepat serta mengurangi risiko human error. |
| Pemantauan Error (Opsional) | Sentry | Membantu tim mendeteksi dan memperbaiki bug pada lingkungan layanan lebih cepat sehingga kualitas website tetap terjaga. |

 # Alur Utama (User Flow)
 
1. User membuka web → klik "Login with Telegram".
2. Telegram Widget mengirim data (id, username, hash) ke backend → backend verifikasi hash pakai bot token → session dibuat.
3. Sistem generate/assign slug unik → user dapat link domain.com/u/username.
4. User sebar link tersebut di bio Instagram/Twitter/story.
5. Pengunjung anonim membuka link → mengisi form pesan → lolos captcha (anti-bot) → submit.
6. Backend menyimpan pesan ke database, terhubung ke user_id pemilik link.
7. Backend memicu Telegram Bot mengirim notifikasi ke chat pemilik: "Ada pesan baru masuk!".
8. User login ke dashboard untuk membaca pesan, dan bisa generate gambar pesan untuk dibagikan ulang ke story sosmed.

# Fitur Sementara yang Terkonsep
1. Anonymous Voice Note
   
     Bukan hanya pesan yang dapat dikirimkan pengirim anonim kepada user penerima, namun pengirim anonim juga dapat mengirimkan sebuah rekaman suara kepada user penerima dengan durasi maksimal audio 60 detik.
   
2. Connection Pooling
   
     Mencegah server mengalami crash akibat error "Too many connections" ketika trafik melonjak tajam. Karena Next.js API          routes mudah membuat banyak koneksi baru setiap request jika tidak di-pool, dan error "too many connections" dapat muncul bahkan di trafik yang belum terlalu besar.

   
