# Tugas Mandiri 7.2 - Pengembangan Fitur Autentikasi Laravel Breeze

Repositori ini dibuat untuk memenuhi pengumpulan Tugas Mandiri 7.2 pada Mata Kuliah Pemrograman Web. Aplikasi ini dikembangkan menggunakan **Laravel 11** dan **Laravel Breeze** sebagai fondasi sistem autentikasi, yang kemudian dimodifikasi untuk menambahkan fitur multi-role serta kustomisasi data profil pengguna.

---

## Tautan Aplikasi (Live Deployment)

Aplikasi telah berhasil di-deploy ke server cloud dan dapat diakses secara publik melalui tautan berikut:
* **Website Utama (Landing Page):** [https://breeze2411531009-production.up.railway.app](https://breeze2411531009-production.up.railway.app)
* **Halaman Dashboard Admin:** [https://breeze2411531009-production.up.railway.app/admin](https://breeze2411531009-production.up.railway.app/admin)

---

##  Kredensial Akun Uji Coba (Akses Admin)

Guna mempermudah proses pengujian seluruh fitur oleh Dosen Penguji, berikut adalah akun dengan hak akses **Admin** yang telah terdaftar di database server produksi:

* **Email:** `admin@gmail.com`
* **Password:** `password` 

---

## Langkah-Langkah Detail Pembuatan & Modifikasi Fitur

Berikut adalah dokumentasi langkah teknis, logika backend, serta file-file utama yang dimodifikasi secara mendalam untuk memenuhi seluruh indikator penilaian:

### 1. Modifikasi Basis Data (Database & Migrations)
* **Menambahkan Kolom Baru:** Membuat file migrasi baru atau memodifikasi file bawaan `database/migrations/xxxx_xx_xx_xxxxxx_create_users_table.php` untuk menambahkan dua field struktural:
  * `no_hp` dengan tipe data `string` (panjang maksimum 15 karakter) untuk menampung nomor telepon.
  * `role` dengan tipe data `enum('user', 'admin')` dan menetapkan nilai bawaan (*default*) berupa `'user'`.
* **Sinkronisasi Database:** Mengeksekusi perintah `php artisan migrate` untuk memperbarui skema tabel `users` pada basis data MySQL.

### 2. Implementasi Tugas 1 — Fitur No. HP pada Form Registrasi & Dashboard
* **Kustomisasi View Registrasi:** Memodifikasi file `resources/views/auth/register.blade.php`. Menyisipkan blok input teks baru dengan memanfaatkan komponen Blade `<x-text-input>` untuk menampung input Nomor HP dari pengguna baru:
  ```blade
  <div>
      <x-input-label for="no_hp" :value="__('No HP')" />
      <x-text-input id="no_hp" class="block mt-1 w-full" type="text" name="no_hp" :value="old('no_hp')" required />
      <x-input-error :messages="$errors->get('no_hp')" class="mt-2" />
  </div>

* **Logika Backend & Validasi Ketat:** Mengubah file `app/Http/Controllers/Auth/RegisteredUserController.php` pada fungsi `store()`. Menambahkan aturan enkapsulasi validasi untuk field `no_hp` agar wajib diisi (`required`), hanya berupa angka (`numeric`), dan memiliki panjang minimal 10 karakter (`min:10`). Data ini kemudian dimasukkan ke dalam query mass-assignment `User::create`.
* **Penayangan pada Dashboard:** Memodifikasi file `resources/views/dashboard.blade.php` dengan menambahkan sintaks pemanggilan objek autentikasi `Auth::user()->no_hp` untuk menampilkan data Nomor HP yang dinamis secara langsung di halaman depan saat user berhasil masuk.

### 3. Implementasi Tugas 2 — Sinkronisasi Halaman Profil Pengguna

* **Validasi Form Edit Profil:** Mengubah file `app/Http/Requests/ProfileUpdateRequest.php` pada fungsi `rules()`. Menambahkan aturan validasi untuk data `no_hp` (`required`, `string`, `max:15`) supaya sistem mengizinkan field tersebut lolos proses pemeriksaan (*form validation*) ketika profil di-update.
* **Modifikasi Form Informasi Profil:** Memodifikasi komponen *partial* view di `resources/views/profile/partials/update-profile-information-form.blade.php`. Menyisipkan input field `no_hp` yang diikat (*binding*) dengan data lama user via properti `:value="old('no_hp', $user->no_hp)"`.
* **Penanganan Duplikasi Komponen:** Memastikan kebersihan struktur HTML di dalam form profil dengan menyusun ulang tag penutup pembungkus `</div>` dan memastikan komponen `<x-primary-button>` (Tombol Save) hanya berjumlah satu agar tata letak tombol tetap simetris dan rapi.

### 4. Implementasi Tugas 3 — Sistem Otentikasi Multi-Role & Halaman Khusus Admin

* **Pembuatan Middleware Otorisasi:** Membuat berkas middleware baru bernama `AdminMiddleware` melalui perintah terminal `php artisan make:middleware AdminMiddleware`.
* **Logika Intersepsi Middleware:** Di dalam file `app/Http/Middleware/AdminMiddleware.php`, dibuat sebuah kondisi pengaman. Jika pengguna belum login atau kolom `role` pada database tidak bernilai `'admin'`, maka request akses akan langsung dihadang (*intercept*) dan dialihkan (*redirect*) kembali menuju halaman login/dashboard dengan pesan peringatan penolakan akses.
* **Registrasi Middleware pada Laravel 11:** Karena Laravel 11 tidak lagi menggunakan file `Kernel.php`, registrasi `AdminMiddleware` dilakukan secara global melalui file `bootstrap/app.php` di dalam konfigurasi `$middleware->alias([...])`.
* **Manajemen Route Khusus Admin:** Mengubah berkas `routes/web.php` dengan mendaftarkan route grup baru untuk URL `/admin`. Route ini diproteksi secara berlapis menggunakan middleware ganda: `auth` (harus login) dan `admin` (harus ber-role admin).
* **Pembuatan Admin Controller:** Membuat `AdminController` guna meng-handle logika bisnis. Di dalam fungsi `index()`, aplikasi memanggil model Eloquent `User::all()` untuk menarik seluruh baris data pengguna dari database cloud, lalu mengirimkannya ke halaman view admin menggunakan fungsi `compact('users')`.
* **Arsitektur Tampilan Tabel Admin:** Membangun file view baru di `resources/views/admin.blade.php` yang berisi struktur tabel HTML responsif berbalut kelas Tailwind CSS untuk menampilkan properti data User (ID, Nama, Email, No HP, dan Role) secara dinamis menggunakan perulangan `@foreach`.
* **Navigasi Navbar Kondisional:** Memodifikasi tata letak menu utama di file `resources/views/layouts/navigation.blade.php`. Menyisipkan tombol navigasi bertuliskan **"Daftar User"** yang dibungkus oleh direktif Blade `@if (Auth::user()->role === 'admin')`. Logika ini menjamin tombol navigasi tersebut hanya akan merender dan muncul di layar jika pengguna yang aktif adalah seorang Admin, sedangkan bagi pengguna biasa (`role === 'user'`) tombol ini sepenuhnya disembunyikan.

---

## Penjelasan Fitur yang Telah Diselesaikan

Seluruh indikator penilaian tugas mandiri telah diselesaikan 100% dan berhasil di-deploy ke lingkungan produksi server cloud (Railway). Sistem proteksi berlapis, enkripsi password, manajemen session, validasi form, hingga visualisasi data tabel user pada halaman admin telah diuji melalui berbagai macam browser (Google Chrome, Microsoft Edge, dll.) dan berjalan secara stabil tanpa adanya kendala teknis ataupun kegagalan sistem.


