# 🚀 Local Development Environment (Docker Compose)

Panduan konfigurasi dan dokumentasi layanan lokal untuk pengembangan aplikasi (Laravel, Vue.js, dll.) yang mencakup PostgreSQL, MySQL, Redis, dan Garage (S3-compatible storage).

---

## 📋 Daftar Layanan & Port Mapping

Berikut adalah daftar kontainer yang berjalan beserta informasi aksesnya:

| Layanan | Container Name | Port Utama (Host:Container) | Port Pendukung | Keterangan / Credentials |
| --- | --- | --- | --- | --- |
| **PostgreSQL 15** | `local_postgres` | `5432:5432` | - | User: `root`<br>

<br>Password: `password`<br>

<br>DB: `postgres` |
| **pgAdmin 4** | `local_pgadmin` | `5050:80` | - | URL: http://localhost:5050<br>

<br>Email: `admin@admin.com`<br>

<br>Password: `password` |
| **MySQL 8.0** | `local_mysql` | `3306:3306` | - | Root Password: `password`<br>

<br>DB: `local_db` |
| **phpMyAdmin** | `local_phpmyadmin` | `8080:80` | - | URL: http://localhost:8080<br>

<br>User: `root`<br>

<br>Password: `password` |
| **Redis 7** | `local_redis` | `6379:6379` | - | No password (default) |
| **Garage S3** | `local_garage` | `3900:3900` (S3 API) | `3901:3901` (RPC) | Endpoint: `http://localhost:3900`<br>

<br>Config: `./volumes/garage/config/garage.toml` |

---

## ⚙️ Cara Menjalankan Layanan

1. **Clone atau letakkan file `docker-compose.yml**` di direktori proyek lokal Anda.
2. Pastikan struktur direktori *volumes* lokal sudah siap (terutama file konfigurasi Garage di `./volumes/garage/config/garage.toml`).
3. Jalankan perintah Docker Compose di terminal:
```bash
docker compose up -d --build

```


4. Periksa status kontainer yang berjalan:
```bash
docker compose ps

```



---

## 📂 Struktur Direktori Volume (Persistence)

Semua data tersimpan secara lokal di dalam folder `./volumes/` agar aman ketika kontainer direstart atau dihapus:

* `./volumes/postgres/` → Data penyimpanan PostgreSQL
* `./volumes/mysql/` → Data penyimpanan MySQL
* `./volumes/redis/` → Data persistence Redis
* `./volumes/garage/data/` → Blok data objek S3 Garage
* `./volumes/garage/meta/` → Metadata database Garage (SQLite)
* `./volumes/garage/config/garage.toml` → File konfigurasi utama Garage