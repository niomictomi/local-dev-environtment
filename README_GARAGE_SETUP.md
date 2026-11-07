# 📦 Garage S3 Storage - Docker Configuration Guide

Panduan lengkap langkah demi langkah untuk menginisialisasi, mengonfigurasi kluster, membuat bucket, dan mengatur akses S3 menggunakan **Garage** di lingkungan Docker lokal.

---

## 🛠️ Prasyarat

Pastikan kontainer Docker Garage Anda sudah berjalan (misalnya dengan nama kontainer `local_garage` atau menggunakan ID kontainer aktif seperti `897aa716f83c`).

---

## 1. Cek Status Node & Ambil Node ID

Pertama, periksa status kluster Garage untuk mendapatkan **Node ID** dari kontainer yang sedang berjalan.

container_ID: 897aa716f83c

```bash
docker exec -it 897aa716f83c garage status

```

**Contoh Output:**

```text
==== HEALTHY NODES ====
ID                Hostname      Address       Tags   Zone  Capacity          DataAvail  ResyncQueue  Version
dcac3d28dbb767fd  897aa716f83c  127.0.0.1:3901             NO ROLE ASSIGNED                     0        v2.5.0

```

> **Catat Node ID Anda:** `dcac3d28dbb767fd`

---

## 2. Assign Layout Kluster

Tetapkan zona, kapasitas penyimpanan, dan peran pada node Anda.

```bash
docker exec -it local_garage garage layout assign dcac3d28dbb767fd --zone local --capacity 100G

```

> *Catatan: Perubahan peran ini masih bersifat *staged* (tertunda) dan perlu diterapkan pada langkah berikutnya.*

---

## 3. Terapkan (Apply) Layout

Finalisasi struktur kluster penyimpanan dengan menerapkan layout versi pertama.

```bash
docker exec -it local_garage garage layout apply --version 1

```

**Contoh Output:**

```text
New cluster layout with updated role assignment has been applied in cluster.
Data will now be moved around between nodes accordingly.

```

---

## 4. Buat Access Key

Buat kunci akses (*Access Key*) baru untuk menghubungkan aplikasi (seperti Laravel) ke Garage.

```bash
docker exec -it local_garage garage key create laravel-key

```

**Simpan Kredensial yang Dihasilkan:**
Berikut contoh kredensial yang dihasilkan (akan berbeda untuk setiap pembuatan):

* **Key ID:** `GK7a52bb4cb52df77ef89d6efb`
* **Secret Key:** `b816e65dafc22a933b6701a059bbee7a3f207181cda843a61233f5c51feb39a7`

---

## 5. Buat Bucket Baru

Buat penyimpanan *bucket* (contoh: `nusabhakti`).

```bash
docker exec -it local_garage garage bucket create nusabhakti

```

---

## 6. Berikan Izin Akses (Allow) Key ke Bucket

Hubungkan *Access Key* yang telah dibuat ke bucket `nusabhakti` dengan hak akses penuh **Read & Write (RW)**.

```bash
docker exec -it local_garage garage bucket allow nusabhakti --read --write --key laravel-key

```

---

## 🚀 Konfigurasi `.env` pada Laravel

Setelah seluruh konfigurasi Garage selesai, masukkan kredensial di atas ke dalam file `.env` proyek Laravel Anda:

```env
FILESYSTEM_DISK=s3

AWS_ACCESS_KEY_ID=GK7a52bb4cb52df77ef89d6efb
AWS_SECRET_ACCESS_KEY=b816e65dafc22a933b6701a059bbee7a3f207181cda843a61233f5c51feb39a7
AWS_DEFAULT_REGION=garage
AWS_BUCKET=nusabhakti
AWS_USE_PATH_STYLE_ENDPOINT=true
AWS_ENDPOINT=http://127.0.0.1:3900

```