# 💤 Panduan Optimasi RAM: "Mode Tidur" Service Lenovo
*Khusus untuk Lenovo IdeaPad Slim 5 14AHP10 (RAM 16GB)*

Dokumen ini berisi langkah-langkah untuk mematikan proses latar belakang yang tidak perlu agar RAM lega untuk menjalankan AI Lokal (**DeepSeek-R1**), namun tetap menjaga kompatibilitas dan garansi tetap aman.

---

## 1. Langkah "Menidurkan" Service (services.msc)
Langkah ini akan mencegah aplikasi Lenovo jalan otomatis saat startup, tetapi aplikasi tetap bisa dibuka kapan saja saat dibutuhkan.

1. Tekan tombol **`Win + R`**, ketik **`services.msc`**, lalu tekan **Enter**.
2. Cari daftar service berikut, klik kanan > **Properties** > Ubah **Startup Type** menjadi **Manual**:
   - `Lenovo Vantage Service`
   - `Lenovo System Interface Foundation Service`
   - `Lenovo Fn and Function Keys Service`
   - `Lenovo Hotkeys`
   - `Lenovo Analytics Service` (Jika ada)
3. Cari service "pihak ketiga" lainnya dan set ke **Manual**:
   - Semua yang ada nama `McAfee` atau `Norton` (Sangat disarankan).
   - `Microsoft Edge Update Service (edgeupdate)`.
   - `Adobe Acrobat Update Service`.

## 2. Membersihkan Startup Apps
1. Tekan **`Ctrl + Shift + Esc`** untuk membuka **Task Manager**.
2. Klik tab **Startup apps** (Ikon speedometer).
3. Klik kanan dan pilih **Disable** pada aplikasi berikut:
   - `Microsoft Teams`
   - `OneDrive`
   - `Spotify` / `Steam` (Jika ada)
   - `Browser Assistant` apa pun.

## 3. Optimasi Grafik (VRAM)
Karena AI menggunakan GPU terintegrasi (Radeon 780M), pastikan Windows memberikan prioritas daya pada aplikasi AI.

1. Buka **Settings** > **System** > **Display** > **Graphics**.
2. Cari **Ollama** atau **VS Code**, klik **Options**.
3. Pilih **High Performance**.

## 4. Cara Verifikasi
Setelah melakukan langkah-langkah di atas, **Restart Laptop Anda** dan cek hasilnya:
- Buka **Task Manager** > Tab **Performance** > **Memory**.
- **Target:** Penggunaan RAM saat *Idle* (diam) harusnya berada di kisaran **25% - 40%**.
- Jika sudah mencapai target, laptop Anda siap menjalankan **`deepseek-r1:14b`** dengan stabil.

## 5. Cara Mengembalikan (Undo)
Jika suatu saat Anda ingin melakukan update BIOS atau cek fitur resmi di Lenovo Vantage:
- Cukup **buka aplikasi Lenovo Vantage** seperti biasa. Windows akan otomatis "membangunkan" service yang dibutuhkan.
- Atau, ulangi langkah nomor 1 dan ubah kembali Startup Type ke **Automatic**.

---

**⚠️ Tips Keamanan:** Jangan pernah men-set service yang ada kata `AMD`, `Radeon`, atau `Windows Search` ke *Disabled*. Cukup set ke *Manual* atau biarkan *Automatic* agar sistem tetap stabil.
