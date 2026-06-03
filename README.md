# Praktikum Laravel 11 Breeze Authentication & Middleware Admin

## Identitas

* Nama: Putri Balqis Afradinata
* NIM: 2411531009

---

# Deskripsi Project

Project ini merupakan implementasi Laravel Breeze Authentication pada Laravel 11 dengan penambahan:

* Login dan Register
* Dashboard User
* Middleware Admin
* Role User dan Admin
* Penambahan field Nomor HP
* Halaman Admin
* Menampilkan daftar seluruh user pada halaman Admin
* Seeder Admin

---

# Fitur Project

## User

* Register akun
* Login
* Logout
* Edit Profile
* Mengubah Password

## Admin

* Login sebagai Admin
* Mengakses halaman Admin
* Melihat seluruh data user yang terdaftar

---

# Langkah-Langkah Pengerjaan Project

## 1. Membuat Project Laravel

```bash
composer create-project laravel/laravel laravel-sisfo11
```

Masuk ke folder project:

```bash
cd laravel-sisfo11
```

---

## 2. Menginstall Laravel Breeze

```bash
composer require laravel/breeze --dev
```

Install Breeze:

```bash
php artisan breeze:install
```

Install dependency frontend:

```bash
npm install
npm run build
```

---

## 3. Konfigurasi Database

Buat database MySQL secara manual melalui phpMyAdmin.

Contoh:

```text
db_breeze
```

Kemudian atur file `.env`:

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=db_breeze
DB_USERNAME=root
DB_PASSWORD=
```

---

## 4. Menjalankan Migrasi

```bash
php artisan migrate
```

---

## 5. Menambahkan Kolom Nomor HP

Membuat migration baru untuk menambahkan field:

```text
no_hp
```

ke tabel users.

Menjalankan migration:

```bash
php artisan migrate
```

---

## 6. Menambahkan Role User dan Admin

Menambahkan field:

```text
role
```

pada tabel users.

Nilai role:

* admin
* user

---

## 7. Membuat Middleware Admin

Membuat middleware:

```bash
php artisan make:middleware AdminMiddleware
```

Middleware digunakan untuk membatasi akses halaman admin hanya untuk user dengan role admin.

---

## 8. Registrasi Middleware pada Laravel 11

Middleware didaftarkan pada file:

```php
bootstrap/app.php
```

---

## 9. Membuat AdminController

Membuat controller:

```bash
php artisan make:controller AdminController
```

Controller digunakan untuk mengambil seluruh data user dari database dan menampilkannya ke halaman admin.

---

## 10. Membuat Halaman Admin

Membuat file:

```text
resources/views/admin.blade.php
```

Halaman admin menampilkan:

* ID User
* Nama
* Email
* Nomor HP
* Role

---

## 11. Menambahkan Route Admin

Menambahkan route:

```php
Route::middleware(['auth', 'admin'])->group(function () {
    Route::get('/admin', [AdminController::class, 'index'])
        ->name('admin');
});
```

---

## 12. Membuat Seeder Admin

Membuat seeder:

```bash
php artisan make:seeder UserSeeder
```

Menjalankan seeder:

```bash
php artisan db:seed --class=UserSeeder
```

Data admin yang dibuat:

Email:

```text
admin@gmail.com
```

Password:

```text
password
```

Role:

```text
admin
```

---

# Cara Menjalankan Project

Clone repository:

```bash
git clone [https://github.com/PutriBalqisAfradinata/breeze_2411531009]
```

Masuk ke folder project:

```bash
cd laravel-sisfo11
```

Install dependency:

```bash
composer install
npm install
```

Copy file environment:

```bash
cp .env.example .env
```

Generate key:

```bash
php artisan key:generate
```

Atur database pada file `.env`.

Jalankan migration:

```bash
php artisan migrate
```

Jalankan seeder admin:

```bash
php artisan db:seed --class=UserSeeder
```

Jalankan aplikasi:

```bash
php artisan serve
```

---

# Akun Admin

Email: admin@gmail.com

Password: password



---

# Deployment

Project telah dicoba untuk dideploy menggunakan Railway.

Link Deployment:

https://breeze2411531009-production.up.railway.app

Status:

* Deployment berhasil dilakukan.
* Login dan koneksi database berhasil berjalan.
* Namun masih terdapat masalah pada pemuatan asset CSS sehingga tampilan antarmuka belum tampil secara optimal.

Meskipun demikian, fungsi utama aplikasi seperti autentikasi, middleware admin, dan koneksi database telah berjalan dengan baik.

