# 🚀 Setup AI Local Developer Environment (AMD Ryzen AI)

Panduan ini untuk memaksimalkan **Ryzen 7 8845HS** agar bisa menjalankan AI secara lokal tanpa limit, tanpa biaya bulanan, dan mampu membaca ratusan file proyek (FE & BE) seperti Claude di Lovable / base44.

---

## 1. Persiapan Hardware (Wajib)
Agar NPU/GPU teralokasi maksimal dan proses tidak *throttling*:
- **Power:** Wajib dicolok **Charger**.
- **Mode:** Tekan **Fn + Q** sampai indikator lampu power berwarna **Merah** (Extreme Performance).
- **Sirkulasi:** Pastikan lubang udara bawah laptop tidak tertutup (jangan di atas kasur).

---

## 2. Instalasi Backend (Mesin AI)
1. Download dan instal **Ollama** di [ollama.com](https://ollama.com).
2. Buka Terminal (PowerShell/CMD), lalu unduh model **Reasoning** (DeepSeek-R1):
   ```bash
   # Gunakan versi 14b (Pas untuk RAM 16GB/32GB)
   ollama pull deepseek-r1:14b
   ```

---

## 3. Instalasi Interface (AI Pair Programmer)
Gunakan **Aider** karena ia yang paling kuat untuk membaca struktur folder besar dan menulis kode langsung ke file.
1. Pastikan Python sudah terinstal, lalu jalankan:
   ```bash
   pip install aider-chat
   ```

---

## 4. Workflow Penggunaan (Langkah Kerja)
Buka terminal tepat di root folder proyek Anda (yang membawahi proyek FE dan BE):

1. **Jalankan Aider:**
   ```bash
   aider --model ollama/deepseek-r1:14b
   ```
2. **Tambah File ke Konteks:**
   Di dalam chat Aider, tambahkan folder/file yang ingin dianalisis:
   ```text
   /add folder_frontend/src
   /add folder_backend/src
   ```
3. **Instruksi Koding:**
   Tanyakan hal kompleks, misalnya:
   *"Cek integrasi API login antara FE dan BE, kenapa di BE tidak menerima header dari FE? Perbaiki keduanya."*
4. **Penerapan Kode:**
   Aider akan menulis kode secara otomatis. Jika sudah sesuai, ketik `/commit` untuk menyimpan ke Git dengan pesan otomatis.

---

## 5. Cara "Dispose" (Bersih-bersih)
- **Stop Proses:** Tekan `Ctrl + C` atau tutup terminal. Penggunaan NPU/GPU/CPU akan langsung kembali ke 0%.
- **Kembalikan Mode:** Tekan **Fn + Q** ke lampu Putih/Biru untuk hemat baterai setelah selesai.
- **Hapus Data (Opsional):** Jika ingin mengosongkan SSD, hapus folder model di `C:\Users\<User>\.ollama`.

---

## 6. Tips Performa
- **Autocomplete:** Jika butuh saran kode per baris (seperti Copilot), instal ekstensi **Continue** di VS Code dan hubungkan ke model `deepseek-coder:1.3b` via Ollama.
- **Indexing:** Saat pertama kali menjalankan Aider/Continue di proyek besar, biarkan dia selesai melakukan *indexing* agar pencarian file menjadi instan.
