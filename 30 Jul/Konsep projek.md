# Arsitektur produksi Sistem E-Learning Adaptif

                                        Internet
                                            │
                                            │ HTTPS (443)
                                            │
                                  +----------------------+
                                  |        Caddy         |
                                  | Reverse Proxy + SSL  |
                                  | HTTP -> HTTPS        |
                                  +----------+-----------+
                                             │
                       +---------------------+----------------------+
                       │                                            │
               Static Files                               Django API
                       │                                            │
                       ▼                                            ▼
                +-------------+                         +----------------------+
                | Static Media|                         | Django + Gunicorn    |
                | /media      |                         | Backend REST API     |
                +-------------+                         | Admin Panel          |
                                                        | Adaptive Quiz Engine |
                                                        +----------+-----------+
                                                                   │
                          +----------------------------+-----------+----------------------+
                          │                            │                                 │
                          ▼                            ▼                                 ▼
                 PostgreSQL                     Redis Cache                     Celery Worker
                 Database                       Session Cache                   Background Task
                 User                           Cache API                       Email
                 Quiz                           Broker                          AI Generate Quiz
                 Nilai                          Rate Limit                      Backup Task
                          │                            ▲                                 │
                          │                            │                                 │
                          └───────────────┬────────────┴─────────────────────────────────┘
                                          │
                                   External Services
                                          │
                          +---------------+---------------+
                          │                               │
                          ▼                               ▼
                      Gemini API                    Groq API
                     AI Tutor                      AI Generator


                         +------------------------------------------+
                         | Monitoring & Backup                      |
                         |------------------------------------------|
                         | Prometheus                               |
                         | Grafana                                  |
                         | Loki                                     |
                         | Promtail                                 |
                         | Uptime Kuma                              |
                         | PostgreSQL Backup                        |
                         +------------------------------------------+

                               Docker Compose
                           Menjalankan seluruh service

                        Portainer (Optional Management)

# Arsitektur Fungsi
```
Guru
 |
 |-- Membuat kelas
 |-- Upload materi
 |-- Membuat soal
 |-- Melihat nilai
 |-- Analitik siswa
 |
 ▼
Django Backend
 │
 ├── Authentication
 ├── Authorization
 ├── Quiz Engine
 ├── Adaptive Learning
 ├── AI Tutor
 ├── Notification
 └── Reporting
 │
 ▼
Database

-------------------------

Siswa

 |-- Login
 |-- Belajar
 |-- Kerjakan Quiz
 L--Tanya AI

 # Topologi Server Debian 13

 Debian 13

-- Docker Engine
-- Docker Compose
-- Caddy
-- Django
-- Gunicorn
-- PostgreSQL
-- Redis
-- Celery
-- Flower
-- Prometheus
-- Grafana
-- Loki
-- Promtail
-- Uptime Kuma
-- Portainer
-- Restic Backup


```
```
flowchart LR

subgraph Guru
A1[Login]
A2[Kelola Kelas]
A3[Upload Materi]
A4[Buat Quiz]
A5[Lihat Nilai]
A6[Analitik]
end

subgraph Siswa
B1[Login]
B2[Belajar]
B3[Kerjakan Quiz]
B4[AI Tutor]
B5[Lihat Progress]
B6[Download Sertifikat]
end

subgraph Backend
C1[Authentication]
C2[Authorization]
C3[Quiz Engine]
C4[Adaptive Learning]
C5[AI Service]
C6[Reporting]
C7[Notification]
end

subgraph Database
D1[(PostgreSQL)]
D2[(Redis)]
end

Guru --> Backend
Siswa --> Backend

Backend --> Database
Backend --> AI

AI[Gemini API / Groq API]
```
