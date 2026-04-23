Prompt:

Build a mobile-first courier logistics management app called "PaketKu" — a WhatsApp-dark-themed 
PWA for field couriers and admins to manage parcel pickup, delivery, and COD finances.

=== TECH STACK ===
React + Vite, Tailwind CSS, Framer Motion, TanStack React Query, React Router DOM, 
shadcn/ui components, Lucide React icons, date-fns.

=== DESIGN SYSTEM ===
Dark theme inspired by WhatsApp:
- Background: #0B141A (wa-darker), #111B21 (wa-dark), #202C33 (wa-chat)
- Accent: #00A884 (wa-teal), #25D366 (wa-green)
- Text muted: #8696A0 (wa-light)
- Border: #233138 (wa-border)
- Bubble sent: #005C4B, bubble received: #1F2C34
Font: Inter. Rounded corners (radius 0.75rem). Max width 512px centered (mobile-first).
Animations: Framer Motion slide-up sheets, fade-in cards, scale-in modals.

=== ENTITIES (DATABASE) ===

1. Paket (Package):
   - resi (string) — tracking number
   - seller_id, seller_nama — sender reference
   - penerima, telepon_penerima — recipient name & phone
   - wilayah_id, wilayah_nama — destination area
   - status: enum [pending, dijemput, disortir, dalam_pengiriman, terantar, gagal, retur]
   - kurir_id, kurir_nama — assigned courier
   - ongkir (number) — shipping fee (courier earnings)
   - pembayaran_status: enum [cod, lunas, lunas_sebagian]
   - lunas_dibayar (number) — amount already paid (for lunas_sebagian)
   - cod_amount (number) — COD value / talangan to seller
   - talangan_disetor (boolean) — whether advance was submitted to admin
   - cod_terkumpul (boolean) — whether COD was collected from buyer
   - foto_paket (string, required) — URL of package label photo
   - foto_bukti (string) — URL of delivery proof photo
   - catatan, berat

2. Kurir (Courier):
   - nama, telepon, area
   - status: enum [aktif, nonaktif, cuti]
   - total_paket, saldo_cod
   - foto_url

3. Seller:
   - nama, telepon, alamat
   - total_talangan_aktif

4. Wilayah (Area):
   - nama, kota
   - ongkir_default (number, default 10000)

5. Activity (audit log):
   - ref_type: enum [paket, kurir]
   - ref_id — ID of referenced entity
   - type: enum [status_change, note, payment, pickup, delivery, return]
   - message, old_status, new_status, amount
   - payment_status: enum [belum_ditagih, sudah_ditagih, sudah_disetor]
   - is_note (boolean)

=== PAGES & NAVIGATION ===

Bottom navigation bar (5 tabs):
Dashboard | Paket | Task | Keuangan | Profil

--- SPLASH SCREEN ---
Full-screen dark splash on app load (2 seconds) with logo animation (Package icon + 
"PaketKu" text + loading dots).

--- DASHBOARD (/) ---
Header: "Dashboard" + "Ringkasan hari ini"
Stats grid (2 columns): Total Paket, Pending, Dalam Proses, Terantar, Gagal, Kurir Aktif
Financial card: Total Ongkir + Total COD (gradient bg)
Quick links to: Kelola Paket, Kelola Kurir, Keuangan

--- PAKET LIST (/paket) ---
Header with search bar + view toggle (list/grid) + FAB (+ button)
Filter tabs: Semua | Pending | Dijemput | Dikirim | Terantar | Gagal | Retur (with counts)
List view: WhatsApp-style chat list — avatar (package photo thumbnail), recipient name, 
  destination, COD badge, ongkir, status dot, relative timestamp
Grid view: 2-column cards with photo, status badge, COD/ongkir tags
Add Package Bottom Sheet (slides up from bottom, 95vh max):
  - REQUIRED: Photo capture of label (camera input) → auto-upload → AI OCR reads:
    resi, penerima, telepon, wilayah, cod_amount → auto-fills form
  - OCR status shown ("AI sedang baca label..." loader → "OCR berhasil" checkmark)
  - No Resi (optional, auto from photo or QR scan)
  - QR scanner button (uses device camera + jsQR library to scan barcodes)
  - Seller dropdown
  - Penerima + No. HP fields (pre-filled from OCR)
  - Wilayah dropdown (auto-selects from OCR result)
  - Ongkir input (thousands shortcut, preset buttons: 7K, 8K, 10K, 12K, 15K, 20K)
  - Payment status selector (3 cards): COD | Lunas | Lunas Sebagian
  - COD amount input (in thousands) — hidden when Lunas
  - Already paid input (for Lunas Sebagian only)
  - Preview box: "Kurir kolek dari pembeli: Rp X" computed dynamically
  - Save button disabled until photo is uploaded

--- PAKET DETAIL (/paket/:id) ---
Full WhatsApp chat-style screen:
Header: back arrow, package photo avatar, recipient name, status badge, COD amount, 
  phone call button, kebab menu
Info bar: wilayah, resi, seller name, payment label + "Kolek: Rp X" amount (dynamic)
Photo strip: package photo + delivery proof photo (tap 2x for fullscreen)
Chat area (scrollable, WhatsApp wallpaper pattern):
  - Activities grouped by date with date separators
  - System messages (status changes, payments) left-aligned dark bubbles with icon
  - Notes right-aligned green bubbles with double-checkmark
  - Status change shows old → new with arrow
Action buttons (bottom, above note input):
  - pending → "Jemput Paket" (teal)
  - dijemput → "Tiba di Sortir" (purple)
  - disortir → "Kirim Sekarang" (orange) + terantar/gagal/retur buttons
  - dalam_pengiriman → camera input "Terantar" (requires photo) + Gagal + Retur
  - done states: no action buttons
Note input: WhatsApp-style text area with send button (tap to send, Enter to send)
Kebab menu: set status shortcuts, delete package

--- KURIR LIST (/kurir) ---
Header + search + FAB
Filter tabs: Semua | Aktif | Nonaktif | Cuti
List items: avatar, name, area, status badge, saldo_cod chip
Add Kurir bottom sheet: nama (required), telepon (required), area fields

--- KURIR DETAIL (/kurir/:id) ---
WhatsApp-style chat screen for each courier's financial history
Header: avatar, name, area + status, phone call, kebab menu
Money summary bar: total recorded money
Filter tabs: Semua | Blm Ditagih | Sdh Ditagih | Sdh Disetor
Chat area: activities filtered by payment status
Kebab menu actions: Tambah Uang COD, Catat Penagihan, Catat Setoran, Hapus Kurir
Note input at bottom

--- TASK KURIR (/task) ---
Courier task management dashboard:
Section 1 — "Perlu Dijemput" (Pending packages):
  - Summary card: total packages + total cash needed for talangan
  - Grouped by seller: each seller card shows package count, cash needed
  - Each seller expandable to show individual packages
  - Per seller: FAB to add new package for that seller (pre-fills seller)
  - Per package row: tap to go to detail, status chip
Section 2 — "Perlu Disortir" (Dijemput packages at hub):
  - List of packages that have been picked up but not yet sorted
  - One-tap "Sortir" button to move to disortir status
Section 3 — "Siap Kirim" (Disortir packages):
  - Packages ready for delivery
  - One-tap "Kirim" button

--- KEUANGAN (/keuangan) ---
Financial summary page:
Top hero card (gradient): "Pendapatan Kamu (Ongkir)" from delivered packages
2x2 summary grid:
  - COD Terkumpul dari Pembeli (green) — from delivered packages
  - Harus Disetor ke Admin (blue) — equals COD terkumpul
  - Talangan Belum Kembali (yellow) — active COD packages
  - Talangan Macet (red) — retur/gagal packages with COD
Setoran rekap box: breakdown of amounts to submit to admin
Paket COD Aktif list: linked list to package details, shows status dot + COD amount
Talangan Macet list: red-themed, shows seller name + amount to collect back
Ringkasan Paket: 3-column count (Aktif, Terantar, Retur/Gagal)

--- PROFIL (/profil) ---
User profile display with avatar, name, email
Menu items: Akun Saya, Notifikasi, Bantuan, Tentang Aplikasi
Logout button

=== FINANCIAL LOGIC ===
- Lunas: courier only collects ongkir at delivery
- COD: courier collects cod_amount + ongkir at delivery  
- Lunas Sebagian: courier collects (cod_amount - lunas_dibayar) + ongkir
- Talangan = advance paid to seller at pickup (= cod_amount)
- Ongkir = courier's earnings per package
- Total setor ke admin = all COD collected from buyers (not ongkir)

=== PACKAGE STATUS FLOW ===
pending → dijemput → disortir → dalam_pengiriman → terantar/gagal/retur
(Each transition logged as Activity with old/new status and timestamp)

=== AI INTEGRATION ===
On photo upload in Add Package form:
- Upload photo via UploadFile API
- Call InvokeLLM with the photo URL
- Prompt: extract resi, penerima, telepon_penerima, wilayah, cod_amount from label
- Auto-fill form fields (only empty fields, never overwrite user input)
- Show loading state during OCR, success checkmark when done

=== QR SCANNER ===
Modal with device camera stream + jsQR library
Real-time frame scanning in animation loop
Viewfinder overlay with animated scan line
Auto-close on successful scan, result fills resi field

=== KEY UX PATTERNS ===
- All sheets animate slide-up from bottom with spring physics
- Photo thumbnails everywhere (list items, detail header)
- Relative timestamps (HH:mm for today, "Kemarin", dd/MM/yy for older)
- All money formatted as "Rp X.XXX.XXX" (Indonesian locale)
- Ongkir input in thousands (type "10" = Rp 10.000) with preset grid
- Loading spinners on all async operations
- Empty states with icon + message
- Pull-to-refresh via React Query invalidation
- iOS momentum scrolling on chat areas
- Scrollbar hidden on filter tabs


=== ALUR BISNIS PAKETKU — JASA KURIR LOKAL ===

AKTOR:
- Admin/Owner  → kelola sistem, terima setoran
- Seller       → titip paket untuk dikirim
- Kurir        → jemput & antar paket
- Pembeli      → terima paket, bayar COD

────────────────────────────────────────
FLOW 1: PENERIMAAN PAKET DARI SELLER
────────────────────────────────────────

Seller datang/hubungi → Kurir/Admin input paket:
  1. Foto label paket (WAJIB) → AI OCR auto-baca data
  2. Tentukan status pembayaran:
     ┌─ COD         → Seller belum dibayar, pembeli bayar penuh saat terima
     ├─ Lunas       → Seller sudah dibayar pembeli, kurir hanya kolek ongkir
     └─ Lunas Sebagian → Pembeli sudah DP, kurir kolek sisa + ongkir
  3. Input cod_amount (harga barang) + ongkir
  4. Sistem catat talangan ke seller = cod_amount
  5. Status paket → PENDING

UANG YANG BERGERAK DI SINI:
  COD     → Kurir/Admin bayar talangan ke seller (cod_amount)
  Lunas   → Tidak ada uang bergerak
  Sebagian → Tidak ada uang bergerak (sudah DP langsung ke seller)

────────────────────────────────────────
FLOW 2: PENJEMPUTAN (PICKUP)
────────────────────────────────────────

Kurir buka Task → lihat daftar seller yang perlu dijemput:
  1. Kurir ke lokasi seller
  2. Ambil semua paket dari seller
  3. Tap "Jemput Paket" per paket → status: PENDING → DIJEMPUT
  4. Kurir bawa paket ke hub/kantor sortir

────────────────────────────────────────
FLOW 3: SORTIR DI HUB
────────────────────────────────────────

Paket tiba di hub:
  1. Tap "Tiba di Sortir" → status: DIJEMPUT → DISORTIR
  2. Paket dipisah per wilayah/kurir antar
  3. Kurir antar ditugaskan
  4. Tap "Kirim Sekarang" → status: DISORTIR → DALAM PENGIRIMAN

────────────────────────────────────────
FLOW 4: PENGIRIMAN KE PEMBELI
────────────────────────────────────────

Kurir antar ke alamat pembeli:
  
  SKENARIO A — BERHASIL:
    1. Kurir ketemu pembeli
    2. Kurir kolek uang dari pembeli:
       - COD          → kolek cod_amount + ongkir
       - Lunas        → kolek ongkir saja
       - Lunas Sebagian → kolek (cod_amount - lunas_dibayar) + ongkir
    3. Foto bukti (wajib) → tap "Terantar"
    4. Status → TERANTAR
    5. cod_terkumpul = true

  SKENARIO B — GAGAL ANTAR:
    1. Pembeli tidak ada / tolak paket
    2. Tap "Gagal" → status → GAGAL
    3. Paket dibawa balik ke hub
    4. Talangan jadi macet (perlu ditagih ke seller)

  SKENARIO C — RETUR:
    1. Paket dikembalikan ke seller
    2. Tap "Retur" → status → RETUR
    3. Talangan macet → tagih ke seller

────────────────────────────────────────
FLOW 5: REKONSILIASI KEUANGAN
────────────────────────────────────────

Akhir hari / periode:
  
  KURIR setor ke Admin:
    Dapat dari pembeli:  cod_amount (tiap paket terantar)
    Simpan untuk diri:   ongkir (pendapatan kurir)
    Setor ke admin:      total COD terkumpul
    
  ADMIN rekap:
    Terima setoran COD dari kurir
    COD dikembalikan ke modal talangan seller berikutnya
    
  TALANGAN MACET (gagal/retur):
    Admin/kurir tagih ke seller
    Seller kembalikan uang talangan
    
RUMUS KURIR:
  Kolek dari pembeli    = cod_amount + ongkir (COD)
                        = ongkir (Lunas)
                        = sisa + ongkir (Sebagian)
  Pendapatan kurir      = ongkir per paket terantar
  Setor ke admin        = total COD terkumpul (bukan ongkir)

────────────────────────────────────────
FLOW 6: MONITORING (ADMIN DASHBOARD)
────────────────────────────────────────

Dashboard real-time:
  - Total paket per status
  - Total COD beredar (talangan aktif)
  - Total ongkir earned
  - Kurir aktif
  - Alert: talangan macet (gagal/retur)
  - Alert: COD belum disetor

Keuangan page:
  - Per kurir: berapa COD dipegang, berapa sudah disetor
  - Paket macet: seller mana yang perlu ditagih
  - Rekap periode: total pendapatan ongkir

────────────────────────────────────────
SUMMARY ALUR UANG
────────────────────────────────────────

Admin/Kurir → [Talangan] → Seller (saat pickup COD)
Pembeli     → [COD+Ongkir] → Kurir (saat delivery)
Kurir       → [COD] → Admin (setor harian)
Kurir       → [Ongkir] → Kantong sendiri ✓
Admin       → [Talangan baru] → Seller (siklus berikutnya)

Seller gagal/retur → wajib kembalikan talangan ke Admin
