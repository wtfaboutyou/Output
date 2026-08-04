# 📋 Dokumen Konsep Proyek — DropMes (Anonymous Messages)

> Dokumen ini ditujukan sebagai penjelasan konsep teknis kepada klien mengenai arsitektur, pilihan teknologi, cara kerja sistem, serta fitur-fitur yang dibangun pada proyek **DropMes**.

---

## 1. Apa Itu DropMes?

**DropMes** adalah platform pesan anonim berbasis web yang memungkinkan pengguna menerima pesan (teks maupun voice note) dari siapa pun secara anonim. Pengguna cukup login melalui **Telegram**, mendapatkan link unik, lalu menyebarkan link tersebut di media sosial. Pengunjung yang membuka link dapat mengirim pesan tanpa perlu membuat akun.

---

## 2. Framework & Tools yang Digunakan

### 2.1 Next.js — Frontend + Backend dalam Satu Framework

| Aspek | Detail |
|-------|--------|
| **Apa** | Framework React full-stack dari Vercel |
| **Versi** | 16.x (App Router) |
| **Peran** | Menangani tampilan website (frontend) sekaligus logika server (API routes) |

**Mengapa Next.js?**
- **Efisiensi biaya & waktu** — Tidak perlu membangun dua proyek terpisah (frontend & backend). Satu codebase mengurus semuanya, sehingga waktu pengembangan lebih singkat dan biaya lebih hemat.
- **Performa tinggi** — Mendukung Server-Side Rendering (SSR) dan Static Generation sehingga halaman tampil lebih cepat di browser pengguna.
- **Ekosistem matang** — Komunitas besar, dokumentasi lengkap, dan banyak library pendukung yang siap pakai.

---

### 2.2 PostgreSQL — Database Utama

| Aspek | Detail |
|-------|--------|
| **Apa** | Database relasional open-source |
| **Versi** | 17 (Alpine) |
| **Peran** | Menyimpan data pengguna, link, pesan, dan voice note |

**Mengapa PostgreSQL?**
- **Struktur data jelas** — Relasi antara user → link → pesan → voice note terdefinisi dengan baik, sehingga data tetap konsisten dan akurat.
- **Skalabel** — Mampu menangani pertumbuhan data dalam jangka panjang tanpa penurunan performa signifikan.
- **Fitur enkripsi bawaan (pgcrypto)** — Pesan dapat dienkripsi langsung di level database tanpa perlu komponen tambahan.

---

### 2.3 Redis — Cache & Antrean Tugas

| Aspek | Detail |
|-------|--------|
| **Apa** | In-memory data store berkecepatan tinggi |
| **Versi** | 7 (Alpine) |
| **Peran** | Antrean tugas (BullMQ), rate limiting, dan pesan sementara (TTL) |

**Mengapa Redis?**
- **Sangat cepat** — Beroperasi di memori, cocok untuk tugas yang membutuhkan respons instan.
- **Multi-fungsi** — Satu komponen menangani tiga kebutuhan sekaligus: antrean konversi voice note, pembatasan spam, dan pesan yang otomatis terhapus.
- **TTL bawaan** — Mendukung penghapusan data otomatis berdasarkan waktu, ideal untuk fitur self-destructing messages.

---

### 2.4 Telegram Bot API — Autentikasi & Notifikasi

| Aspek | Detail |
|-------|--------|
| **Apa** | API resmi Telegram untuk bot |
| **Peran** | Login pengguna (Telegram Login Widget) dan pengiriman notifikasi pesan baru |

**Mengapa Telegram?**
- **Tanpa akun/password baru** — Pengguna login cukup satu tap melalui Telegram, mengurangi hambatan pendaftaran.
- **Notifikasi gratis** — Setelah login, kanal notifikasi otomatis tersedia tanpa biaya SMS gateway atau push notification berbayar.

---

### 2.5 Cloudflare — CDN, DNS, & Keamanan

| Aspek | Detail |
|-------|--------|
| **Apa** | Platform keamanan dan performa web |
| **Layanan** | DNS, CDN, Turnstile Captcha |

**Mengapa Cloudflare?**
- **Gratis** — Paket free sudah mencakup CDN, DNS, proteksi DDoS, dan captcha.
- **Turnstile Captcha** — Anti-bot tanpa mengganggu pengalaman pengguna (tidak perlu klik gambar).

---

### 2.6 Tools Pendukung Lainnya

| Tool | Fungsi | Alasan Dipilih |
|------|--------|----------------|
| **Prisma ORM** | Mengelola skema & query database | Type-safe, migrasi otomatis, mempercepat development |
| **BullMQ** | Antrean tugas background (konversi audio) | Andal, berbasis Redis, mendukung retry otomatis |
| **PgBouncer** | Connection pooling database | Mencegah crash "Too many connections" saat trafik tinggi |
| **Docker** | Containerization seluruh layanan | Deployment konsisten, mudah diperbarui dan dipulihkan |
| **Nginx** | Reverse proxy + SSL termination | Mengelola HTTPS dan routing ke aplikasi Next.js |
| **Prometheus + Grafana** | Monitoring server real-time | Dashboard visual, deteksi masalah lebih awal, gratis |
| **OpenTelemetry + Grafana Tempo** | Distributed tracing | Melacak perjalanan request dari ujung ke ujung |
| **Uptime Kuma** | Pemantauan ketersediaan | Notifikasi otomatis jika layanan down |
| **GitHub Actions** | CI/CD pipeline | Otomatisasi build & deployment, mengurangi human error |

---

## 3. Cara Kerja Sistem

### 3.1 Alur Pendaftaran & Login

```
Pengguna membuka website
        │
        ▼
Klik "Login with Telegram"
        │
        ▼
Telegram Widget mengirim data (id, username, hash)
        │
        ▼
Server memverifikasi hash menggunakan bot token
        │
        ▼
Session dibuat → Pengguna masuk ke Dashboard
        │
        ▼
Sistem otomatis membuat link unik: domain.com/u/username
```

**Penjelasan:** Pengguna tidak perlu membuat akun atau mengingat password baru. Cukup satu kali tap melalui Telegram, sistem langsung memverifikasi identitas dan membuatkan sesi login yang aman.

---

### 3.2 Alur Pengiriman Pesan Anonim (Teks)

```
Pengunjung anonim membuka link (domain.com/u/username)
        │
        ▼
Mengisi form pesan teks
        │
        ▼
Cloudflare Turnstile memverifikasi bukan bot
        │
        ▼
Rate Limiter (Redis) mengecek batas pengiriman
        │
        ▼
Pesan dienkripsi menggunakan pgcrypto
        │
        ▼
Pesan terenkripsi disimpan ke PostgreSQL
        │
        ▼
Telegram Bot mengirim notifikasi ke pemilik link:
"🔔 Ada pesan baru masuk!"
        │
        ▼
Pemilik membuka Dashboard → membaca pesan (didekripsi saat dibaca)
```

**Penjelasan:** Setiap pesan melewati beberapa lapisan keamanan: captcha anti-bot, pembatasan frekuensi pengiriman, dan enkripsi sebelum disimpan. Meskipun terjadi kebocoran database, isi pesan tidak bisa dibaca langsung karena sudah terenkripsi.

---

### 3.3 Alur Pengiriman Voice Note

```
Pengunjung anonim merekam voice note (maks. 60 detik)
        │
        ▼
File audio diunggah ke server
        │
        ▼
Tugas konversi masuk ke antrean BullMQ (dikelola Redis)
        │
        ▼
Worker mengambil tugas → FFmpeg mengonversi audio ke format ringan
        │
        ▼
File hasil konversi disimpan kembali ke Cloudflare R2
        │
        ▼
Status voice note diperbarui: PENDING → PROCESSING → READY
        │
        ▼
Telegram Bot mengirim notifikasi ke pemilik
```

**Penjelasan:** Proses konversi audio dilakukan di background (tidak membuat pengguna menunggu). BullMQ memastikan jika konversi gagal, tugas akan otomatis dicoba ulang. Seluruh proses ini dapat dipantau melalui distributed tracing (OpenTelemetry).

---

### 3.4 Alur Self-Destructing Messages

```
Pengunjung mengirim pesan dengan opsi:
  ├── "Hapus setelah dibaca" (READ_ONCE)
  └── "Hapus setelah X menit/jam" (TTL)
        │
        ▼
Pesan disimpan ke PostgreSQL + TTL dicatat di Redis
        │
        ▼
Jika READ_ONCE: pesan otomatis dihapus setelah pemilik membacanya
Jika TTL: Redis menghapus data saat waktu habis,
          lalu cleanup worker menghapus dari PostgreSQL secara berkala
```

**Penjelasan:** Fitur ini memberikan kontrol tambahan kepada pengirim. Pesan benar-benar hilang secara permanen, bukan sekadar disembunyikan.

---

## 4. Fitur-Fitur Utama

### 🔗 Link Unik Personal
Setiap pengguna mendapatkan link unik (misalnya `domain.com/u/johndoe`) yang dapat dibagikan ke bio Instagram, Twitter, story, atau media sosial lainnya. Pengunjung cukup membuka link untuk mengirim pesan anonim.

### 💬 Pesan Anonim Terenkripsi
Pesan yang dikirim oleh pengunjung bersifat anonim — identitas pengirim tidak dicatat oleh sistem. Selain itu, isi pesan dienkripsi sebelum disimpan ke database menggunakan **pgcrypto**, sehingga privasi terjaga bahkan di level server.

### 🎙️ Anonymous Voice Note
Selain teks, pengunjung juga dapat mengirimkan rekaman suara (voice note) dengan durasi maksimal 60 detik. File audio dikonversi ke format yang lebih ringan secara otomatis di background.

### 🔔 Notifikasi Instan via Telegram
Setiap kali ada pesan baru masuk, pemilik link langsung menerima notifikasi di Telegram. Tidak perlu membuka website untuk mengecek — notifikasi datang secara real-time.

### 💣 Self-Destructing Messages
Pengirim dapat memilih agar pesan otomatis terhapus permanen setelah dibaca sekali (Read Once) atau setelah durasi tertentu (TTL). Fitur ini terinspirasi dari aplikasi chat populer dan memperkuat aspek privasi.

### 🖼️ Generate Gambar Pesan
Pemilik link dapat mengubah pesan anonim yang diterima menjadi gambar (image card) yang siap dibagikan ulang ke story Instagram/Twitter, mendorong interaksi dan viralitas.

### 🛡️ Anti-Spam & Anti-Bot
Sistem dilindungi oleh **Cloudflare Turnstile** (captcha ramah pengguna) dan **Rate Limiting** (pembatasan frekuensi pengiriman per IP), mencegah penyalahgunaan layanan oleh bot atau spammer.

### ⚡ Connection Pooling
**PgBouncer** bertindak sebagai "resepsionis" yang mengelola koneksi ke database. Ini mencegah server crash akibat error "Too many connections" ketika banyak pengguna mengakses secara bersamaan.

### 📊 Monitoring & Observability
- **Prometheus + Grafana** memantau kondisi server (CPU, RAM, request per detik) secara real-time dalam dashboard visual.
- **OpenTelemetry + Grafana Tempo** melacak perjalanan setiap request dari ujung ke ujung, sehingga jika ada bottleneck (misal konversi audio lambat), tim dapat melihat persis di step mana masalah terjadi.
- **Uptime Kuma** mengirim notifikasi otomatis jika layanan mengalami gangguan.

### 🐳 Containerized Deployment
Seluruh layanan (Next.js, PostgreSQL, Redis, PgBouncer, Grafana, Tempo) dikemas dalam **Docker containers** yang dikelola oleh **Docker Compose**. Ini memastikan:
- Deployment yang konsisten di berbagai environment
- Proses pembaruan dan rollback yang mudah
- Pemulihan cepat jika terjadi gangguan

### 🔄 CI/CD Otomatis
**GitHub Actions** mengotomatisasi proses build dan deployment. Setiap kali ada perubahan kode yang di-push, sistem secara otomatis membangun, menguji, dan men-deploy versi terbaru — mengurangi risiko human error.

---

## 5. Arsitektur Sistem (Ringkasan Visual)

```
            [Pengunjung Anonim]              [Pemilik Akun]
                    |                               |
             isi form pesan                 login Telegram
                    |                               |
                    v                               v
      ──────> [Cloudflare: DNS, CDN, TLS, Turnstile Captcha] <──────
                                |
                                v
                ┌──────────────────────────────────┐
                │         Server (Debian 13)       │
                │   Nginx (reverse proxy + SSL)    │
                │                                  │
                │   [Next.js App] ──> [PostgreSQL] │
                │         |      \                 │
                │         |    [PgBouncer]          │
                │         v                        │
                │   [Redis + BullMQ]               │
                │         |                        │
                │         v                        │
                │   [Telegram Bot: notifikasi]     │
                │                                  │
                │   [Prometheus] ──> [Grafana]     │
                │   [OpenTelemetry] ──> [Tempo]    │
                └──────────────────────────────────┘
```

---

## 6. Keunggulan Arsitektur Ini

| Aspek | Keunggulan |
|-------|------------|
| **Biaya** | Mayoritas tools bersifat open-source dan gratis (Cloudflare Free, Grafana OSS, Let's Encrypt) |
| **Keamanan** | Multi-layer: Cloudflare DDoS protection → Turnstile captcha → Rate limiting → Enkripsi pesan |
| **Performa** | CDN global, connection pooling, background processing untuk tugas berat |
| **Skalabilitas** | PostgreSQL + PgBouncer siap menangani pertumbuhan pengguna jangka panjang |
| **Maintainability** | Docker containers memudahkan update, rollback, dan pemulihan sistem |
| **Observability** | Monitoring end-to-end dari metrics server hingga tracing per-request |

---

> **Catatan:** Dokumen ini menggambarkan konsep dan arsitektur yang direncanakan. Fitur-fitur dapat berkembang atau disesuaikan seiring berjalannya pengembangan berdasarkan kebutuhan dan masukan klien.
