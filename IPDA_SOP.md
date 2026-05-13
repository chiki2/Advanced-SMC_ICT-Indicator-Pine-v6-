SOP Trading Intraday: Protokol IPDA

TAHAP 1: Filter Waktu (The Foundation)
IPDA beroperasi berdasarkan waktu sebelum harga. Jangan menyentuh chart di luar jam ini.
* 00:00 – 08:00 (Waktu New York): Fase observasi Midnight Open. Amati pergerakan Asia untuk menentukan bias.
* 08:30 – 11:00 (Waktu New York): AM Session (Kill Zone Utama). Fokus utama untuk mencari entry.
* 13:30 – 16:00 (Waktu New York): PM Session. Hanya digunakan jika target pagi belum tercapai atau terjadi reversal.

TAHAP 2: Analisis Narasi (Daily Bias & Look-Back)
1. Gunakan siklus Look-Back IPDA untuk menentukan ke mana harga akan dikirim hari ini.Cek Chart Daily: Lihat area tertinggi/terendah dalam 20 hari terakhir.
2. Tentukan Draw on Liquidity (DOL): Ke mana algoritma ingin pergi?
* Apakah mencari Liquidity (Old High/Low, Equal Highs/Lows)?
* Apakah mencari Rebalancing (Mengisi Fair Value Gap yang belum tersentuh)?
3. Proyeksi PO3: Jika bias Bullish, bayangkan skema: Open -> Manipulasi ke bawah -> Ekspansi ke atas.

TAHAP 3: Identifikasi Setup di Kill Zone (Execution)
Lakukan langkah ini hanya saat memasuki jendela waktu Kill Zone:
1. Stop Raid / Judas Swing: Tunggu harga menyapu likuiditas (mengambil High/Low sesi sebelumnya atau Asia High/Low).
2. Market Structure Shift (MSS): Tunggu harga berbalik arah dengan agresif hingga menembus struktur terdekat (menandakan algoritma berubah arah).
3. Displacement: Pastikan pergerakan saat MSS meninggalkan Fair Value Gap (FVG) atau Order Block. Ini adalah jejak kaki algoritma.

TAHAP 4: Entry & Risk Management
1. Titik Entry: Pasang Limit Order di area FVG atau Order Block yang terbentuk saat Displacement.
2. Stop Loss (SL): Letakkan di atas/bawah lilin yang membentuk Order Block atau di titik tertinggi/terendah swing manipulasi.
3. Take Profit (TP):
     * TP 1: Target likuiditas terdekat (Opposite Liquidity).
     * TP 2: Target utama berdasarkan narasi Daily (DOL).

TAHAP 5: Manajemen Posisi
* Pindahkan SL ke Breakeven setelah harga mencapai rasio 1:1 atau setelah terjadi Market Structure Shift baru yang searah dengan posisi Anda.
* JANGAN menambah posisi jika waktu Kill Zone sudah berakhir.
