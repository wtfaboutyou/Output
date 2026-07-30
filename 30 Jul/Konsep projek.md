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
```


 # Topologi Server Debian 13

```
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
