# 🛍️ Ruang Mode — Toko Online Fashion

**Ruang Mode** adalah aplikasi web **e-commerce fashion** berbasis **Laravel 13** yang menghadirkan pengalaman belanja online lengkap: katalog produk dengan varian (ukuran/warna), keranjang belanja berbasis session, checkout dengan multi-metode pembayaran (Transfer Bank, E-Wallet, COD) beserta simulasi konfirmasi otomatis, serta panel admin lengkap untuk manajemen kategori, produk, pelanggan, pesanan, dan laporan penjualan yang dapat diekspor ke Excel.

> **Tujuan Projek:** Tugas akhir mata kuliah Pemrograman Sistem Informasi — sistem e-commerce fashion dengan checkout multi-langkah, auto-konfirmasi pembayaran online, dan laporan penjualan yang dapat diekspor ke `.xlsx`.

---

## 📋 Daftar Isi

- [Fitur Utama](#-fitur-utama)
  - [Fitur Pelanggan (Customer)](#-fitur-pelanggan-customer)
  - [Fitur Admin](#-fitur-admin)
  - [Simulasi Pembayaran](#-simulasi-pembayaran)
- [Tech Stack](#-tech-stack)
- [Struktur Database](#-struktur-database)
- [Flow Aplikasi](#-flow-aplikasi)
- [Activity Diagram (UML)](#-activity-diagram-uml)
- [Struktur Route](#-struktur-route)
- [Instalasi & Setup](#-instalasi--setup)
- [Akun Demo](#-akun-demo)
- [Struktur Direktori](#-struktur-direktori)
- [Catatan Development](#-catatan-development)
- [Lisensi](#-lisensi)

---

## ✨ Fitur Utama

### 👤 Fitur Pelanggan (Customer)

| Fitur | Deskripsi |
|-------|-----------|
| **Registrasi** | Daftar akun (nama, email, password + konfirmasi, no. HP, alamat). Otomatis login & diarahkan ke toko |
| **Login & Remember Me** | Login dengan email & password. Redirect otomatis: admin → dashboard, customer → toko |
| **Logout** | Keluar dengan aman (session di-invalidate & token di-regenerate) |
| **Katalog Produk** | Grid semua produk aktif, **pencarian nama**, **filter kategori**, pagination 12 produk/halaman, urut terbaru |
| **Produk Unggulan** | Menampilkan 4 produk featured (`is_featured`) di beranda toko |
| **Detail Produk** | Gambar, harga normal & harga diskon (dicoret), stok, SKU, bahan, **pilih ukuran & warna**, atur jumlah, produk terkait (4 dari kategori sama) |
| **Keranjang Belanja** | Berbasis **session**, tombol +/− quantity, hapus item, subtotal real-time, **badge jumlah item** di navbar |
| **Varian di Keranjang** | Produk sama dengan ukuran/warna berbeda = item keranjang berbeda (key `product_id\|size\|color`) |
| **Checkout** | Halaman checkout dengan alamat pengiriman (otomatis terisi dari profil), catatan opsional, dan pilihan metode pembayaran |
| **3 Metode Pembayaran** | 🔵 Transfer Bank, 🟢 E-Wallet (bayar online, otomatis terkonfirmasi), 🟡 COD (bayar di tempat) |
| **Ongkos Kirim** | Flat Rp 15.000 per transaksi (dihitung otomatis di ringkasan) |
| **Riwayat Pesanan** | Daftar semua pesanan pribadi (10/halaman) dengan badge status berwarna |
| **Detail Pesanan** | Rincian item, alamat, referensi pembayaran, dan **timeline status** pesanan |
| **Keamanan Pesanan** | User hanya bisa melihat pesanannya sendiri (403 jika mengakses pesanan orang lain) |
| **Profil Saya** | Edit nama, no. HP, dan alamat |
| **Ganti Password** | Verifikasi password lama + password baru minimal 6 karakter dengan konfirmasi |

### 👑 Fitur Admin

| Fitur | Deskripsi |
|-------|-----------|
| **Dashboard** | Statistik: total produk, total pesanan, **total pendapatan** (dari pesanan `paid`), total pelanggan |
| **Dashboard — Kategori Terpopuler** | Bar chart relatif 5 kategori dengan jumlah produk terbanyak |
| **Dashboard — Pesanan Terbaru** | 5 pesanan terakhir beserta status |
| **Dashboard — Stok Menipis** | Peringatan 4 produk dengan stok ≤ 10 |
| **Manajemen Kategori** | CRUD kategori (nama, deskripsi, gambar, aktif/nonaktif), pencarian, jumlah produk per kategori, pagination 10 |
| **Manajemen Produk** | CRUD produk lengkap: nama, kategori, SKU (unik), harga, harga diskon (harus < harga normal), stok, ukuran, warna, bahan, gambar, **aktif** & **unggulan (featured)** |
| **Filter Produk** | Pencarian nama/SKU, filter kategori, filter status aktif/nonaktif, pagination 12 |
| **Detail Produk (Admin)** | Menampilkan daftar item pesanan yang memuat produk tersebut |
| **Manajemen Pelanggan** | CRUD pelanggan (nama, email unik, password, no. HP, alamat), pencarian nama/email/HP, lihat jumlah pesanan per pelanggan, pagination 10 |
| **Manajemen Pesanan** | Daftar semua pesanan, **pencarian** (nomor pesanan / nama pelanggan), **filter status** & **filter status pembayaran**, pagination 10 |
| **Detail Pesanan (Admin)** | Info pelanggan, rincian item + varian, alamat kirim, catatan, referensi pembayaran, total |
| **Buat Pesanan Manual** | Admin dapat membuat pesanan untuk pelanggan: pilih pelanggan, produk (stok > 0), jumlah, ukuran/warna, ongkir, diskon, metode bayar. **Stok otomatis berkurang** |
| **Update Status Pesanan** | Admin memilih bebas salah satu dari 5 status (`pending`, `processing`, `shipped`, `completed`, `cancelled`). Jika status diubah menjadi `cancelled` (dari status non-cancelled), **stok otomatis dikembalikan** |
| **Update Status Pembayaran** | `unpaid → paid → refunded` |
| **Hapus Pesanan** | Menghapus pesanan & **mengembalikan stok** produk terkait |
| **Laporan Penjualan** | Filter periode **Hari Ini / Bulan Ini / Tahun Ini**: total pendapatan, total pesanan, rata-rata nilai order |
| **Laporan — Produk Terlaris** | Top 10 produk berdasarkan jumlah terjual & pendapatan (hanya pesanan `paid`) |
| **Laporan — Kategori Terpopuler** | Kategori dengan penjualan terbanyak |
| **Laporan — Pesanan Terbaru** | 10 pesanan terakhir periode terpilih |
| **Laporan — Stok Menipis** | 10 produk dengan stok ≤ 10 |
| **Ekspor Excel** | Download laporan periode terpilih ke `.xlsx` (ringkasan, produk terlaris, detail pesanan) via PhpSpreadsheet |

### 💳 Simulasi Pembayaran

Aplikasi menyimulasikan konfirmasi pembayaran otomatis untuk metode online:

| Metode | Perilaku | Status Awal | Referensi |
|--------|----------|-------------|-----------|
| 🔵 **Transfer Bank** | Langsung terkonfirmasi (simulasi) | `processing` / `paid` | `VA-YYYYMMDD-XXXXXXXX` (Virtual Account) |
| 🟢 **E-Wallet** | Langsung terkonfirmasi (simulasi) | `processing` / `paid` | `TRX-YYYYMMDD-XXXXXXXX` (Transaction ID) |
| 🟡 **COD** | Bayar saat barang diterima | `pending` / `unpaid` | — |

---

## 🛠 Tech Stack

| Layer | Teknologi |
|-------|-----------|
| **Framework** | Laravel 13 (`laravel/framework ^13.8`) |
| **PHP** | PHP ^8.3 — butuh **ekstensi `zip`** (untuk ekspor Excel) |
| **Database** | SQLite (default bawaan projek) — mendukung MySQL/MariaDB via `.env` |
| **Frontend** | Blade Templating + Tailwind CSS 4 |
| **Build Tool** | Vite 8 (`laravel-vite-plugin`) |
| **Ikon** | Inline SVG (tanpa library eksternal) |
| **Excel Export** | PhpSpreadsheet 5.8 |
| **Testing** | PHPUnit 12.5 |
| **Penyimpanan Gambar** | Upload ke `storage/app/public/` atau URL eksternal |

---

## 🗄️ Struktur Database

### Entity Relationship Diagram

```
┌─────────────┐     ┌──────────────┐     ┌──────────────┐
│    Users    │1──N│    Orders    │1──N│  OrderItems  │
├─────────────┤     ├──────────────┤     ├──────────────┤
│ id          │     │ id           │     │ id           │
│ name        │     │ order_number │     │ order_id(FK) │
│ email       │     │ user_id (FK) │     │ product_id   │
│ role        │     │ subtotal     │     │ product_name │
│  (admin /   │     │ shipping_cost│     │ price        │
│   customer) │     │ discount     │     │ quantity     │
│ phone       │     │ total        │     │ subtotal     │
│ address     │     │ status       │     │ size         │
│ password    │     │ payment_method│    │ color        │
└─────────────┘     │ payment_status│    └──────────────┘
       │            │ payment_ref  │            │
       │            │ shipping_addr│            │ N
       │            │ notes        │            │
       │            │ created_at   │            │ 1
       │            └──────────────┘     ┌──────────────┐
       │                                 │   Products   │
       │                                 ├──────────────┤
       │                                 │ id           │
       │                                 │ category_id  │
       │                                 │ name, slug   │
       │                                 │ sku          │
       │                                 │ description  │
       │                                 │ price        │
       │                                 │ sale_price   │
       │                                 │ stock        │
       │                                 │ size, color  │
       │                                 │ material     │
       │                                 │ image        │
       │                                 │ is_active    │
       │                                 │ is_featured  │
       │                                 └──────┬───────┘
       │                                        │ N
       │                                        │ 1
       │                                 ┌──────────────┐
       │                                 │  Categories  │
       │                                 ├──────────────┤
       │                                 │ id           │
       │                                 │ name, slug   │
       │                                 │ description  │
       │                                 │ image        │
       │                                 │ is_active    │
       └─────────────────────────────────└──────────────┘
```

### Tabel & Deskripsi

| Tabel | Fungsi |
|-------|--------|
| **users** | Data user dengan kolom `role` (`admin` / `customer`), `phone`, `address`. Sumber data pelanggan (dikelola lewat menu Pelanggan = filter `role = customer`) |
| **categories** | Kategori produk: nama, slug otomatis, deskripsi, gambar, status aktif |
| **products** | Produk: harga normal & harga diskon, stok, varian (`size`, `color`, `material`), gambar, `is_active`, `is_featured` |
| **orders** | Pesanan: subtotal, ongkir, diskon, total, status pesanan, metode & status pembayaran, referensi pembayaran, alamat kirim, catatan |
| **order_items** | Item pesanan: **snapshot** nama produk, harga, dan varian saat checkout (riwayat aman walau produk berubah) |
| **customers** | Skema cadangan (legacy) — saat ini pengelolaan pelanggan menggunakan tabel `users` |

### Nilai Enum Penting

| Kolom | Nilai |
|-------|-------|
| `orders.status` | `pending`, `processing`, `shipped`, `completed`, `cancelled` |
| `orders.payment_method` | `transfer`, `cod`, `e-wallet` |
| `orders.payment_status` | `unpaid`, `paid`, `refunded` |
| `users.role` | `admin`, `customer` |

---

## 🔄 Flow Aplikasi

### Flow Pelanggan

```
Landing (/) ─┬─ belum login ──▶ Login / Register
             └─ sudah login ──▶ Toko (/store) atau Dashboard (/dashboard untuk admin)

BERBELANJA
──────────
Katalog Produk  ──▶  Detail Produk  ──▶  Keranjang  ──▶  Checkout  ──▶  Pesanan Dibuat
 • cari produk        • pilih ukuran      • +/− qty        • alamat        • nomor ORD-xxxx
 • filter kategori    • pilih warna       • hapus item     • catatan       • referensi VA/TRX
 • 12/halaman         • atur jumlah       • subtotal       • metode bayar  • cart dikosongkan
                      • produk terkait                     • ongkir 15rb

SETELAH CHECKOUT
────────────────
Halaman Konfirmasi Pesanan ──▶ Riwayat Pesanan ──▶ Detail + Timeline Status
                                       │
                                       ▼
                              Profil: edit data & ganti password
```

**Alur Status Pesanan (sisi pelanggan):**
```
COD     : Pending ──▶ Processing ──▶ Shipped ──▶ Completed
          (unpaid)    (unpaid)       (unpaid)     (paid)

Online  : Processing ──▶ Shipped ──▶ Completed
          (paid)         (paid)       (paid)

Semua   : ──▶ Cancelled (stok dikembalikan)
```

### Flow Admin

```
Login (admin@fashionstore.id) ──▶ Dashboard
                                    │
        ┌───────────────┬───────────┼──────────────┬──────────────┐
        ▼               ▼           ▼              ▼              ▼
    Kategori         Produk     Pelanggan       Pesanan        Laporan
      CRUD            CRUD        CRUD        (full control)  (per periode)
        │               │           │              │              │
        │               │           │              ├─ Buat pesanan manual
        │               │           │              │  (stok berkurang)
        │               │           │              ├─ Update status
        │               │           │              │  (cancel = stok kembali)
        │               │           │              ├─ Update pembayaran
        │               │           │              └─ Hapus (stok kembali)
        │               │           │                             │
        ▼               ▼           ▼                             ▼
   nama, gambar,   +SKU, harga,  nama, email,      Filter: today / monthly / yearly
   aktif/nonaktif  diskon, stok, password, HP,     ─▶ Produk terlaris, kategori populer,
                   varian, gambar alamat           pesanan terbaru, stok menipis
                                                            │
                                                            ▼
                                                   📥 Export Excel (.xlsx)
```

**Alur Status Pesanan (sisi admin):**
Admin dapat memilih langsung salah satu dari 5 status (tidak harus berurutan):

```
PENDING · PROCESSING · SHIPPED · COMPLETED · CANCELLED
```

Khusus **CANCELLED**: bila status lama bukan `cancelled`, stok seluruh item
pesanan otomatis dikembalikan.

---

## 📐 Activity Diagram (UML)

Dokumentasi alur bisnis dalam bentuk **UML Activity Diagram** tersedia pada file:

**`activity-diagram-toko-online.drawio`** — 1 halaman cover/legenda + **16 activity diagram** (satu untuk setiap use case utama pelanggan & admin).

### Cara Membuka

1. Buka [draw.io](https://app.diagrams.net) atau aplikasi **draw.io Desktop**
2. **File → Open From → Device** → pilih `activity-diagram-toko-online.drawio`
3. Berpindah halaman lewat **tab di bawah kanvas**; ekspor per halaman via **File → Export as → PNG / PDF**

### Daftar Diagram

| Hal. | Diagram | Hal. | Diagram |
|------|---------|------|---------|
| 00 | Cover, daftar diagram & legenda notasi | 09 | Dashboard Admin |
| 01 | Registrasi Akun | 10 | Kelola Kategori |
| 02 | Login & Logout | 11 | Kelola Produk |
| 03 | Katalog & Detail Produk | 12 | Kelola Pelanggan |
| 04 | Keranjang Belanja (Session Cart) | 13 | Buat Pesanan Manual (Admin) |
| 05 | Checkout & Pembayaran | 14 | Update Status Pesanan |
| 06 | Riwayat & Detail Pesanan | 15 | Kelola Pembayaran & Hapus Pesanan |
| 07 | Edit Profil Pelanggan | 16 | Laporan Penjualan & Ekspor Excel |
| 08 | Ganti Password | | |

### Notasi yang Digunakan

- **Swimlane** per partisipan: **Pelanggan**, **Admin**, dan **Sistem** (proses internal aplikasi)
- **Start node** (●), **Action**, **Decision** (dengan label **Ya/Tidak**), **Merge**, dan **End node** (◉)
- Isi diagram disusun berdasarkan **implementasi kode aktual** pada controller, route, dan view

---

## 🧭 Struktur Route

### 🔓 Guest

| Method | URI | Action |
|--------|-----|--------|
| GET | `/login` | `Auth\LoginController@showLoginForm` |
| POST | `/login` | `Auth\LoginController@login` |
| GET | `/register` | `Auth\RegisterController@showRegistrationForm` |
| POST | `/register` | `Auth\RegisterController@register` |

### 🔐 Auth (semua user login)

| Method | URI | Action |
|--------|-----|--------|
| POST | `/logout` | `Auth\LoginController@logout` |
| GET | `/` | Redirect otomatis: admin → `/dashboard`, customer → `/store` |

### 👤 Toko / Customer (prefix `/store`, middleware `auth`)

| Method | URI | Action |
|--------|-----|--------|
| GET | `/store` | `StoreController@index` — katalog produk |
| GET | `/store/product/{product}` | `StoreController@show` — detail produk |
| POST | `/store/cart/add/{product}` | `StoreController@addToCart` |
| GET | `/store/cart` | `StoreController@cart` |
| POST | `/store/cart/update` | `StoreController@updateCart` |
| DELETE | `/store/cart/{id}` | `StoreController@removeFromCart` |
| GET | `/store/checkout` | `StoreController@checkout` |
| POST | `/store/order` | `StoreController@placeOrder` |
| GET | `/store/orders` | `StoreController@myOrders` |
| GET | `/store/orders/{order}` | `StoreController@showOrder` |
| GET | `/store/profile` | `StoreController@profile` |
| PUT | `/store/profile` | `StoreController@updateProfile` |
| PUT | `/store/profile/password` | `StoreController@changePassword` |

### 👑 Admin (middleware `auth` + `admin`)

| Method | URI | Action |
|--------|-----|--------|
| GET | `/dashboard` | `DashboardController@index` |
| — | `/categories` | `CategoryController` (resource: index, create, store, show, edit, update, destroy) |
| — | `/products` | `ProductController` (resource lengkap) |
| — | `/customers` | `CustomerController` (resource lengkap) |
| — | `/orders` | `OrderController` (resource lengkap) |
| PATCH | `/orders/{order}/status` | `OrderController@updateStatus` |
| PATCH | `/orders/{order}/payment` | `OrderController@updatePayment` |
| GET | `/reports` | `ReportController@index` — laporan per periode |
| GET | `/reports/export-excel` | `ReportController@exportExcel` — download `.xlsx` |

> Akses halaman admin oleh non-admin akan ditolak dengan **403 Forbidden** (via `AdminMiddleware`).

---

## 🚀 Instalasi & Setup

### Prasyarat

- **PHP ≥ 8.3** dengan ekstensi **`zip`** aktif *(wajib — dipakai PhpSpreadsheet untuk ekspor Excel; aktifkan `extension=zip` di `php.ini`)*
- **Composer**
- **Node.js & NPM** (untuk build aset Vite)
- Tanpa perlu server database — projek memakai **SQLite** secara default

### Langkah Instalasi

```bash
# 1. Clone repositori
git clone https://github.com/ajisaputra007/Ruang-Mode.git
cd Ruang-Mode

# 2. Install dependensi PHP
composer install

# 3. Copy file environment
copy .env.example .env      # Windows
# cp .env.example .env      # Linux / macOS

# 4. Generate application key
php artisan key:generate

# 5. Buat file database SQLite
# Windows (PowerShell):
New-Item -ItemType File -Path database/database.sqlite -Force
# Linux / macOS:
touch database/database.sqlite

# 6. Jalankan migrasi + seeder (data demo siap pakai)
php artisan migrate --seed

# 7. Buat storage symlink (agar gambar upload bisa diakses)
php artisan storage:link

# 8. Install & build aset frontend
npm install
npm run build

# 9. Jalankan development server
php artisan serve
```

Buka **http://127.0.0.1:8000** lalu login dengan akun demo di bawah.

### Cara Cepat (satu perintah)

```bash
composer setup
```
*(menjalankan `composer install`, generate key, migrate, `npm install`, dan `npm run build` sekaligus)*

### Mode Development (hot reload)

```bash
composer dev
```
*(menjalankan server + queue listener + log viewer + Vite dev server secara bersamaan)*

### Menggunakan MySQL / MariaDB (opsional)

Ubah `.env`:

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=ruang_mode
DB_USERNAME=root
DB_PASSWORD=
```

Lalu jalankan kembali `php artisan migrate --seed`.

---

## 👥 Akun Demo

Semua akun menggunakan password: **`password`**

| Role | Email |
|------|-------|
| **Admin** | `admin@fashionstore.id` |
| Customer | `rina@email.com` |
| Customer | `ahmad@email.com` |
| Customer | `siti@email.com` |
| Customer | `budi@email.com` |
| Customer | `dewi@email.com` |
| Customer | `raka@email.com` |

Seeder juga membuat **5 kategori**, **13 produk fashion**, dan **8 pesanan contoh** dengan berbagai status.

---

## 📁 Struktur Direktori

```
Ruang-Mode/
├── activity-diagram-toko-online.drawio   (dokumentasi UML — 17 halaman)
├── app/
│   ├── Http/
│   │   ├── Controllers/
│   │   │   ├── Auth/
│   │   │   │   ├── LoginController.php
│   │   │   │   └── RegisterController.php
│   │   │   ├── CategoryController.php
│   │   │   ├── CustomerController.php
│   │   │   ├── DashboardController.php
│   │   │   ├── OrderController.php
│   │   │   ├── ProductController.php
│   │   │   ├── ReportController.php
│   │   │   └── StoreController.php
│   │   └── Middleware/
│   │       ├── AdminMiddleware.php
│   │       └── CustomerMiddleware.php
│   └── Models/
│       ├── Category.php   ├── Order.php     ├── Product.php
│       ├── Customer.php   ├── OrderItem.php └── User.php
├── database/
│   ├── migrations/        (users, cache, jobs, categories, products,
│   │                       customers, orders, order_items, +role, +image)
│   ├── factories/         (UserFactory)
│   └── seeders/
│       └── DatabaseSeeder.php   (admin, 6 customer, 5 kategori, 13 produk, 8 pesanan)
├── resources/views/
│   ├── auth/          (login, register, layout)
│   ├── categories/    (index, create, edit)
│   ├── customers/     (index, create, edit)
│   ├── layouts/       (app = admin, store = toko)
│   ├── orders/        (index, show, create)
│   ├── products/      (index, show, create, edit)
│   ├── reports/       (index)
│   ├── store/         (index, show, cart, checkout, orders, order-show, profile)
│   ├── dashboard.blade.php
│   └── welcome.blade.php
└── routes/
    └── web.php
```

---

## 🧠 Catatan Development

### Varian Produk (Ukuran/Warna)
- Kolom `size` & `color` menyimpan string (mis. `S/M/L` atau `Merah, Biru`) yang di-*split* di halaman detail produk
- Keranjang memakai key `{product_id}|{size}|{color}` sehingga varian berbeda dihitung sebagai item terpisah
- `order_items` menyimpan **snapshot** nama, harga, ukuran, dan warna saat checkout — riwayat pesanan tetap utuh walau data produk berubah kemudian

### Keranjang Berbasis Session
- Keranjang disimpan di **session** (bukan database) — cepat dan sederhana
- Otomatis kosong setelah checkout berhasil atau logout
- `updateCart` menangani kasus data terkirim sebagai string JSON (cache browser lama)

### Auto-Konfirmasi Pembayaran Online
- **Transfer Bank:** referensi `VA-YYYYMMDD-XXXXXXXX`
- **E-Wallet:** referensi `TRX-YYYYMMDD-XXXXXXXX`
- Status pesanan langsung `processing` + `paid` (simulasi pembayaran berhasil)
- Referensi disimpan di kolom `payment_reference` (ditambahkan via migrasi `add_payment_reference_to_orders_table`)

### Manajemen Stok Otomatis
- **Pesanan manual admin** (`OrderController@store`) mengurangi stok tiap produk yang dipesan
- **Checkout pelanggan tidak mengurangi stok** — jumlah hanya divalidasi agar tidak melebihi stok tersedia
- **Batal pesanan (`cancelled`)** mengembalikan stok produk (hanya bila status lama bukan `cancelled`)
- **Hapus pesanan** mengembalikan stok produk

### Model & Accessor
- `Product::effective_price` → otomatis memakai `sale_price` bila ada
- `Product::is_on_sale` → cek apakah produk sedang diskon
- `Order::status_label`, `status_color`, `payment_status_label`, `payment_method_label` → label & warna badge siap pakai di Blade
- `Product::image` & `Category::image` → otomatis mendukung URL eksternal atau path `storage/`
- Nomor pesanan otomatis dibuat via model event: `ORD-{unik}`

### Layout
- `layouts/app.blade.php` → layout panel admin
- `layouts/store.blade.php` → layout toko (Tailwind CSS, sticky navbar, badge keranjang)

---

## 📄 Lisensi

Projek ini dibuat untuk tujuan pendidikan. Dibangun dengan **[Laravel](https://laravel.com)** — framework PHP open-source dengan lisensi MIT.
