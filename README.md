# PRISMA (Platform Rekapitulasi Informasi Sekolah & Manajemen Anggaran)

**PRISMA** (**P**latform **R**ekapitulasi **I**nformasi **S**ekolah & **M**anajemen **A**nggaran) adalah solusi ERP terintegrasi berbasis Frappe/ERPNext yang dirancang khusus untuk mengelola administrasi keuangan dan operasional di lingkungan Taman Kanak-Kanak (TK) di bawah naungan Yayasan.

Aplikasi ini bertujuan untuk mentransformasi pencatatan manual (Spreadsheet) menjadi sistem digital yang terintegrasi, transparan, dan akuntabel.

---

## ✨ Fitur Utama

1.  **Siklus Pendapatan Terintegrasi**: Pengelolaan pembayaran SPP, DSP (cicilan), dan donasi dengan fitur pelacakan sisa tagihan otomatis.
2.  **Manajemen Payroll (Penggajian)**: Kalkulasi gaji guru otomatis berdasarkan data kehadiran, menggantikan penghitungan manual.
3.  **Point of Sale (POS) Koperasi**: Sistem kasir untuk penjualan seragam, ATK, dan merchandise sekolah.
4.  **Alur Persetujuan (Workflow)**: Validasi dokumen berjenjang (Diperiksa -> Diketahui -> Disetujui) pada *Journal Entry* untuk memastikan keamanan transaksi.
5.  **Dashboard Visual**: Grafik pemantauan performa keuangan dan operasional secara real-time.

---

## 🛠️ Teknologi yang Digunakan

-   **Core Framework**: Frappe Framework & ERPNext (v14/v15)
-   **Database**: MariaDB
-   **Cache**: Redis
-   **Deployment**: Docker & Docker Compose
-   **Frontend**: Frappe Desk & Awesomebar

---

## 🚀 Panduan Instalasi (Development Mode via Docker)

Panduan ini menggunakan file konfigurasi Docker `pwd.yml` dan langsung memanfaatkan *site default* (`frontend`) agar aplikasi dapat diakses dengan mudah melalui port 8080.

### Prasyarat Penting!
Sebelum melakukan *clone* aplikasi ini, pastikan mesin Docker Anda sudah menjalankan *container* Frappe/ERPNext (`frappe_docker-backend-1`).

### Langkah-Langkah Instalasi

**1. Nyalakan Container Docker:**
Pastikan ekosistem Frappe Anda sudah menyala.
```bash
docker compose -f pwd.yml up -d
```

**2. Tarik Aplikasi PRISMA ke dalam Container:**
Gunakan perintah `bench get-app` untuk mengunduh kode sumber dari GitHub langsung ke dalam *container* backend.
```bash
docker exec -it frappe_docker-backend-1 bench get-app https://github.com/DhafinQ/erp-prisma-docker.git
```

**3. Instal ERPNext Terlebih Dahulu (Wajib):**
Karena PRISMA bergantung pada modul akuntansi bawaan (seperti *Journal Entry*), aplikasi `erpnext` wajib diinstal lebih dulu ke *site* `frontend`.
```bash
docker exec -it frappe_docker-backend-1 bench --site frontend install-app erpnext
```

**4. Instal Aplikasi PRISMA:**
Setelah *core* ERP terpasang, instal PRISMA untuk menempelkan fitur dan kustomisasi Anda.
```bash
docker exec -it frappe_docker-backend-1 bench --site frontend install-app prisma
```

**5. Sinkronisasi Database & Konfigurasi (Migrate):**
Perintah ini akan menyuntikkan konfigurasi kustom (seperti *Workflow* "Persetujuan Dokumen") dari folder `fixtures` ke dalam database MariaDB.
```bash
docker exec -it frappe_docker-backend-1 bench --site frontend migrate
```

**6. Bersihkan Cache (Opsional tapi Direkomendasikan):**
```bash
docker exec -it frappe_docker-backend-1 bench --site frontend clear-cache
```

**7. Akses Aplikasi:**
Buka browser dan akses **`http://localhost:8080`**.

---

## ⚠️ Catatan Penting untuk Developer

* **Sifat Container (Ephemeral):** Pada mode *development* menggunakan `get-app`, kode aplikasi disimpan di memori sementara *container*. Jika Anda melakukan `docker compose restart` atau `down`, aplikasi `prisma` akan hilang dari daftar *site* (meskipun data di database aman). Anda cukup menjalankan ulang langkah 2 hingga 5 untuk mengembalikannya.
* **Struktur Repositori:** Pastikan repositori `prisma` di GitHub *hanya* berisi struktur inti aplikasi Frappe (file `setup.py` atau `pyproject.toml` harus berada langsung di *root* folder, bukan di dalam sub-folder).
* **Update Konfigurasi:** Jika Anda menambahkan *Workflow*, *Custom Field*, atau fitur UI lainnya, pastikan untuk mendaftarkannya di `hooks.py`, jalankan `bench export-fixtures`, dan `git push` perubahan tersebut agar rekan tim Anda mendapatkan struktur terbaru saat melakukan *migrate*.

---

## 📖 Tutorial Penggunaan Singkat

1.  **Pencatatan Keuangan (Persetujuan Dokumen):** Buka **Journal Entry** -> Buat entri baru -> Simpan. Tombol **Periksa** akan muncul di kanan atas untuk memulai alur persetujuan.
2.  **Transaksi Koperasi (POS):** Buka menu **POS Awesome** -> Pilih barang -> Lakukan pembayaran -> Sistem otomatis memotong stok dan mencatat pemasukan.

