Berikut adalah revisi README.md yang telah diterjemahkan ke dalam Bahasa Indonesia dengan penyesuaian istilah teknis agar tetap akurat namun mudah dipahami:

---

# 🚀 Binance Autotrade – Sistem Trading Futures Berbasis Order Block

Sistem perdagangan Binance Futures yang sepenuhnya otomatis dan dibangun menggunakan **n8n**. Mengimplementasikan deteksi *Order Block* kustom, manajemen risiko terstruktur, serta logika *Stop Loss* / *Take Profit* dinamis menggunakan API Binance (permintaan bertanda tangan HMAC SHA256).

---

## 📌 Ikhtisar

Proyek ini adalah alur kerja (*workflow*) perdagangan otomatis ujung-ke-ujung yang berfungsi untuk:

* Mendeteksi *Bullish* & *Bearish Order Blocks*.
* Mengeksekusi entri *LIMIT*.
* Menempatkan *Stop Loss* & *Take Profit* berbasis algoritma.
* Menggeser *Stop Loss* secara dinamis (*Breakeven* & *Profit Lock*).
* Mengelola *leverage* secara otomatis.
* Mencegah pesanan ganda atau konflik pesanan.
* Memantau posisi terbuka secara terus-menerus.

Dibangun menggunakan:

* 🧠 Logika struktur pasar kustom (*Order Block* + Struktur ABC).
* 🔁 Otomatisasi *workflow* n8n.
* 🔐 Binance Futures REST API (*signed*).
* 📊 Deteksi struktur pasar pada *timeframe* 1 Jam (1H).

---

# 🧠 Logika Strategi

### 1️⃣ Deteksi Order Block

Sistem menganalisis data OHLCV dan mengidentifikasi:

* *Volume Pivot High* (PHV).
* *Market Structure Break* (Bullish / Bearish).
* *Order Block* yang valid.
* *Mitigated Order Blocks* (dihapus secara otomatis jika sudah tersentuh).

**Bullish OB:**

* Entri pada harga rata-rata OB.
* *Stop Loss* di bawah area OB.

**Bearish OB:**

* Entri pada harga rata-rata OB.
* *Stop Loss* di atas area OB.

---

### 2️⃣ Struktur Pasar ABC & Logika Take Profit

Setelah mendeteksi *Order Block*, sistem menghitung:

* **A** → *Major Swing*
* **B** → *Impulse*
* **C** → *Retracement*

**Rasio Retracement:** `Retracement = BC / AB`

**Klasifikasi:**

* Retracement ≤ 0.382 → *Middle continuation* (lanjutan tengah).
* Retracement > 0.382 → *Early structure* (struktur awal).

**Proyeksi Take Profit:**

* 1.0 Fibonacci.
* 0.786 Fibonacci (kondisional).

---

### 3️⃣ Pergerakan Stop Loss Dinamis

*Stop Loss* menyesuaikan secara otomatis berdasarkan keuntungan yang belum terealisasi (*unrealized profit*):

| Profit % | Tindakan |
| --- | --- |
| ≥ 2% | Pindahkan SL ke harga Entri (*Breakeven*) |
| ≥ 4% | Kunci keuntungan (*Lock Profit*) sebesar 2% |

Sistem menyertakan logika *anti-downgrade* (SL tidak akan pernah bergerak mundur/turun untuk posisi Buy).

---

# ⚙️ Arsitektur Sistem

### 🔹 Modul Utama

1. **Pengambilan Data Pasar**: Mengambil data 1H OHLC dari Binance Futures serta analisis volume & struktur.
2. **Mesin Order Block**: Mendeteksi OB, memfilter blok yang sudah termitigasi, dan mengembalikan daftar OB aktif.
3. **Pemantau Posisi**: Memeriksa posisi terbuka, mengidentifikasi sisi (BUY/SELL), dan mengambil data PnL.
4. **Mesin Risiko**: Menghitung TP melalui struktur ABC, menyesuaikan SL secara dinamis, dan memastikan rasio RR yang tepat.
5. **Lapisan Eksekusi**: Eksekusi pesanan *LIMIT*, `STOP_MARKET` via `/fapi/v1/algoOrder`, `TAKE_PROFIT_MARKET`, dan kontrol *leverage*.
6. **Pembersihan Pesanan**: Mendeteksi pesanan limit ganda dan menghapus SL/TP yang sudah usang/kadaluwarsa.

---

# 🔐 Keamanan

* Semua *endpoint* privat ditandatangani menggunakan **HMAC SHA256**.
* Menggunakan parameter `timestamp` dan `recvWindow` untuk mencegah *replay attacks*.
* API Keys disimpan dengan aman (Google Sheets / Environment Variables).

---

# 📂 Teknologi yang Digunakan

* **n8n**: Mesin otomatisasi (*Automation engine*).
* **HTTP Request nodes**: Untuk komunikasi dengan API Binance.
* **Crypto node**: Untuk enkripsi HMAC SHA256.
* **Code nodes**: Logika pemrograman JavaScript.
* **Google Sheets**: Penyimpanan data akun & log.

---

# 📊 Fitur Unggulan

✅ Deteksi *Order Block* otomatis.
✅ *Take Profit* berbasis struktur pasar.
✅ *Stop Loss* dinamis (Trailing).
✅ Manajemen *AlgoOrder*.
✅ Kontrol *Leverage* otomatis.
✅ Deteksi posisi aktif.
✅ Logika pencegahan pesanan limit ganda.
✅ Eksekusi terkontrol risiko.

---

# 🧪 Contoh Alur Perdagangan

1. Deteksi *Bullish* OB.
2. Pasang *LIMIT BUY* di harga rata-rata OB.
3. Pasang *STOP_MARKET* di bawah area OB.
4. Hitung struktur ABC.
5. Pasang *TAKE_PROFIT_MARKET*.
6. Jika harga naik +2% → SL pindah ke *breakeven*.
7. Jika harga naik +4% → SL mengunci profit 2%.

**Berjalan sepenuhnya otomatis.**

---

# ⚠️ Penafian Risiko (Risk Disclaimer)

Proyek ini dibuat hanya untuk tujuan pendidikan dan penelitian.
Perdagangan *cryptocurrency futures* melibatkan risiko tinggi. Gunakan dengan tanggung jawab Anda sendiri.
