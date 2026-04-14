# Trader Tutorial

Panduan ini ditulis untuk membantu trader memakai `Advanced SMC / ICT Indicator` tanpa harus membaca kode.

Jika Anda ingin versi sangat singkat dan lebih visual, lihat [TRADER_CHEAT_SHEET.md](./TRADER_CHEAT_SHEET.md).

## Tujuan Indikator

Indikator ini membantu membaca:
- struktur market
- liquidity dan sweep
- session context
- zona OB / FVG
- OTE aktif
- bias, regime, dan execution state

Indikator ini bukan mesin kepastian. Fungsinya adalah merapikan konteks supaya trader lebih cepat memilih:
- tunggu
- pantau retest
- entry aktif
- manage trade

## Cara Mulai Cepat

Untuk pemakaian paling mudah:
- pilih profile `Standard` untuk chart desktop
- pilih profile `Compact` jika layar kecil
- nyalakan `Show context dashboard`
- nyalakan `Show trading sessions on chart`

Jika ingin paling ketat ala ICT:
- pilih profile `Strict Confirm`
- biarkan `Show active OTE overlay` tetap hidup
- `Zone display mode` tetap bebas Anda pilih, misalnya `Full`
- `Compact dashboard mode` juga tetap bisa dipakai tanpa mengubah engine strict

Formula baca `Strict Confirm` sekarang sengaja dibuat eksplisit:
- `ERL sweep`
- `displacement / MSS`
- `PD-array / OTE`
- `DOL`
- `killzone`

Catatan penting:
- `volume tinggi` tidak dipakai sebagai syarat wajib
- alasannya agar indikator tetap lean dan tidak overfit ke feed volume broker tertentu

Timeframe eksekusi yang didukung:
- `M1`
- `M5`
- `M15`
- `M30`

Di luar timeframe itu, indikator berhenti agar tetap jujur sebagai intraday setup engine.

## Struktur Panel Input

Panel input sekarang dibagi ringkas menjadi:
- `Profile`
- `Structure`
- `Liquidity`
- `Zones`
- `Context`
- `Dashboard`
- `Risk`

Prinsipnya:
- yang terlihat di panel adalah keputusan trader-facing
- parameter engine internal sudah dibekukan di kode agar tidak mendorong bias dan overtuning

Catatan penting tentang preset:
- `Strict Confirm` = engine validasi setup
- `Zone display mode` = gaya visual zona
- `Compact dashboard mode` = gaya panel

Jadi Anda bisa memakai kombinasi seperti:
- `Strict Confirm + Full`
- `Strict Confirm + Focused`
- `Strict Confirm + Full + Compact dashboard`

tanpa mengubah engine strict itu sendiri

## Cara Membaca Chart

Urutan baca yang sehat:
1. baca struktur
2. lihat sesi aktif
3. cari zona aktif
4. lihat dashboard
5. eksekusi hanya jika state sudah masuk akal

## 1. Struktur Market

Yang tampil di chart:
- `HH / HL / LH / LL`
- `BOS / MSS`
- marker inducement dan sweep jika diaktifkan

Maknanya:
- `HH / HL` menunjukkan struktur bullish
- `LH / LL` menunjukkan struktur bearish
- `BOS` berarti kelanjutan struktur
- `MSS` berarti potensi perubahan karakter / shift

Prinsip baca:
- jangan mulai dari zona dulu
- mulai dari struktur dulu

## 2. Trading Sessions

Visual sesi sekarang terdiri dari:
- box range sesi
- garis `High` dan `Low` sesi yang memanjang ke kanan

Sesi yang dipakai:
- `Asia`
- `London`
- `New York`

Maknanya:
- box menunjukkan di mana range sesi terbentuk
- garis High/Low menunjukkan batas sesi yang masih relevan sebagai liquidity

Catatan penting:
- logika sesi memakai `New York time`
- sumbu bawah chart tetap mengikuti timezone chart TradingView Anda

Jadi kalau chart Anda memakai `Asia/Jakarta`, posisi sesi di bawah chart bisa terlihat bergeser, tetapi itu normal karena:
- engine sesi = `America/New_York`
- tampilan waktu chart = timezone lokal chart

## 3. Liquidity

Liquidity yang penting dibaca di chart:
- garis High/Low sesi
- `EQH / EQL`
- protected high / protected low
- HTF structure high / low

Catatan:
- `previous source high / low` dan `previous day / week / month high / low` masih dipakai engine
- tetapi visualnya sudah disederhanakan agar chart tidak mengulang informasi yang sama dua kali

Jadi sekarang:
- session lines = representasi liquidity sesi
- engine masih tetap memakai keluarga liquidity internal untuk analisa setup

## 4. Zona OB / FVG

Chart menampilkan zona secara selektif:
- `OB`
- `Breaker`
- `FVG`

Cara membacanya:
- zona terpilih adalah yang paling relevan untuk konteks saat ini
- zona non-terpilih tetap bisa ada, tetapi tampil lebih tenang
- label yang paling menonjol biasanya ada pada zona terpilih

Prinsip:
- jangan anggap semua box adalah entry
- utamakan zona yang searah dengan struktur, bias, dan session context
- dalam profile `Strict Confirm`, indikator sekarang memprioritaskan `FVG` dulu
- jika tidak ada `FVG` yang valid, `OB` dipakai sebagai fallback POI

Tambahan konteks baru:
- `iFVG` di indikator ini berarti `Inversion FVG`
- `iFVG` muncul saat `FVG` lama gagal lalu flip fungsi
- untuk sekarang `iFVG` dipakai sebagai `context-only`, bukan entry engine utama
- jadi `iFVG` membantu membaca pergantian delivery, tetapi tidak otomatis menggantikan `FVG / OB` terpilih

Jika nama setup di dashboard diikuti:
- `Q1`
- `Q2`
- `Q3`

itu berarti `Setup Grade`:
- `Q1` = kualitas dasar
- `Q2` = kualitas menengah
- `Q3` = kualitas terbaik

Grade ini dibangun dari snapshot kualitas setup:
- response / confirm pada POI terpilih
- displacement
- ATR expansion

Jadi `Q` membantu menilai mutu setup, tetapi bukan jaminan entry.

## 5. Dealing Range dan Decision Bands

Indikator juga menampilkan:
- `ICT Dealing Range High`
- `ICT Equilibrium`
- `ICT Dealing Range Low`

Area ini membagi chart menjadi:
- premium
- equilibrium
- discount

Cara pakai:
- bullish context lebih sehat jika mencari harga di discount / bawah equilibrium
- bearish context lebih sehat jika mencari harga di premium / atas equilibrium

## 6. OTE

`OTE` tidak selalu tampil.

OTE hanya muncul saat:
- ada leg aktif yang valid
- ada context shift / MSS yang relevan
- OTE belum invalid / belum selesai dipakai

Artinya:
- jika `Show active OTE overlay = true` tapi chart tidak berubah, biasanya memang tidak ada OTE aktif

Cara baca:
- OTE adalah area retracement optimal
- OTE bukan level permanen
- OTE dipakai sebagai konfirmasi tambahan, bukan alasan tunggal entry

## 7. Risk Cues

Jika `Show stop cues` diaktifkan, chart dapat menampilkan:
- entry cue
- stop cue
- target cue

Label kanan:
- `SL L` = stop untuk setup long aktif
- `SL S` = stop untuk setup short aktif

Catatan:
- risk cues hanya tampil jika setup sudah masuk state yang layak dipantau, misalnya `Armed`, `Active`, atau `Managed`
- kalau semua masih `Waiting`, wajar jika tidak ada SL/TP live di chart

## Cara Membaca Dashboard Full

Dashboard full memiliki 4 kolom:
- `Field`
- `Market`
- `Long`
- `Short`

### Exec

Menunjukkan execution state sisi long dan short.

State yang umum:
- `Waiting` = belum ada alasan entry
- `Confirming` = setup ada, tetapi konfirmasi strict belum lengkap
- `Armed` = setup mulai siap, tunggu retest / trigger
- `Active` = entry sedang aktif / sangat dekat
- `Managed` = trade sudah berjalan, fokus kelola
- `Completed` = setup selesai
- `Expired` = setup terlalu lama / basi
- `Invalidated` = setup batal

### Bias

Merangkum:
- bias chart sekarang
- HTF bias
- regime bias

Format singkat seperti:
- `Bull/Bear/RBear Str`

Cara baca:
- bagian pertama = bias aktif chart
- bagian kedua = bias HTF
- bagian ketiga = regime

Kalau tidak searah, berarti setup lebih berisiko atau bersifat countertrend.

### Pref

Menunjukkan arah yang saat ini lebih layak diperhatikan:
- `Long`
- `Short`
- `Wait`
- kadang `Balanced`

Jika `Wait`, artinya chart belum memberi setup yang cukup bersih.

### Z / E

Menunjukkan:
- `Z` = valid `POI` / zone
- `E` = level entry

Gunanya:
- trader bisa melihat area valid
- lalu melihat trigger price yang sedang dipakai model

Catatan:
- `POI` = `Point of Interest`
- dalam strict mode, `POI` yang paling disukai adalah `FVG`
- `OB` tetap bisa dipakai jika tidak ada `FVG` yang lebih layak

### SL / TP

Menunjukkan:
- stop loss
- target utama

Catatan:
- stop sekarang berbasis struktur + hybrid previous-session volatility
- target biasanya menuju liquidity terdekat yang masih relevan

### RR / Cf

Menunjukkan:
- `RR` = reward / risk
- `Cf` = confidence

Cara baca:
- gunakan sebagai pembanding antar setup
- jangan dipakai sendirian tanpa struktur dan session context
- jika sisi tertentu sudah `Invalidated` atau `Completed`, detail ini bisa diredam menjadi `-`
- pada compact mode, label `Ctx L/S` berarti angka yang tampil masih context strength, belum setup yang actionable

### Draw

Menunjukkan:
- `Draw` = `DOL` / external liquidity target utama
- `TP` = probabilitas model rule-based

Ini membantu melihat apakah target dekat / jauh dan seberapa kuat kualitas setup.

Cara baca:
- `DOL` = `Draw on Liquidity`, yaitu external draw utama
- jika ada level liquidity yang jelas di atas/bawah harga, indikator akan memakainya
- jika tidak ada `DOL` external yang layak, strict confirmation tidak akan semudah itu memberi status siap entry

### Life

Format:
- `Age / touch`

Contoh:
- `5b / Untouched`
- `13b / Touched`

Maknanya:
- `Age` = umur setup dalam jumlah bar
- `Touched` = zona sudah disentuh

Semakin tua setup, biasanya semakin lemah.

### Sess/Cfm

Menunjukkan:
- mode session otomatis
- source session yang sedang dipakai
- timezone basis session
- status konfirmasi long dan short

Contoh:
- `A-ICT LDN>AS | NY TZ`

Artinya:
- sekarang model berada di sesi London
- previous source yang dipakai adalah Asia
- pembacaan sesi berbasis New York time

Kolom Long/Short pada baris ini sekarang menunjukkan status konfirmasi:
- `Standard` = profile standard, tanpa strict confirmation
- `Refine` = context belum cukup rapi
- `KZ Wait` = context sudah baik, menunggu timing killzone
- `Ready` = konfirmasi strict sudah lengkap

Dalam strict mode, ringkasan ini juga membawa jalur setup singkat:
- `FVG > DOL` = flow paling dekat ke ICT yang dicari indikator
- `OB > DOL` = setup masih valid, tetapi POI yang dipakai adalah order block fallback
- `iCtx` = ada `iFVG` aktif yang sedang memberi konteks tambahan
- `iCtx+` = ada `iFVG` aktif dan posisinya selaras dengan setup yang sedang dibaca

Jika sisi `Long` atau `Short` sudah tidak actionable lagi:
- baris `Sess/Cfm` untuk sisi itu akan ditulis `Retired`
- ini sengaja supaya dashboard tidak terus membahas konfirmasi untuk setup yang sudah selesai, invalid, atau basi

Status strict sekarang dibaca lebih eksplisit:
- `Need ERL` = belum ada sweep external liquidity yang cukup
- `Need MSS` = sweep sudah ada, tetapi displacement / MSS belum cukup
- `Need PD` = sweep dan MSS sudah ada, tetapi lokasi POI / OTE belum rapi
- `Need DOL` = context sudah rapi, tetapi external draw belum layak
- `KZ Wait` = context lengkap, menunggu timing killzone
- `Ready` = flow strict lengkap

### Next

Menunjukkan:
- mode efektif
- pasangan timeframe execution / context / narrative
- status OTE
- aksi berikutnya yang disarankan model

Contoh:
- `Wait confirmation`
- `Monitor retest`
- `Entry active`
- `Manage trade`
- `Wait next setup`

Ini adalah ringkasan aksi praktis paling cepat dibaca.

Jika Anda melihat teks seperti:
- `Eff Focused`
- `Eff Minimal`

itu berarti yang ditampilkan adalah mode visual efektif setelah profile diterapkan, bukan sekadar nilai input mentah.

## Cara Membaca Dashboard Compact

Dashboard compact dipakai untuk layar kecil.

Row penting:
- `Biases`
- `Setup`
- `Zone`
- `E / SL / TP`
- `Exec`
- `R / DOL`
- `Life`
- `Mode`
- `Sessions`

Prinsip pakai:
- compact mode dipakai untuk keputusan cepat
- jika butuh detail penuh, lihat dashboard full

Catatan penting:
- jika compact menulis setup seperti `Bullish OB | Q2 | Ctx`, itu berarti ada kandidat dengan `Setup Grade Q2` yang masih sedang terbentuk
- `Ctx` berarti setup itu masih `context-only`, belum actionable untuk entry
- `Ctx+i` berarti ada kandidat setup context-only dan `iFVG` aktif sedang menambah konteks
- `Ctx+i+` berarti `iFVG` aktif itu juga selaras dengan setup kandidat yang sedang terbaca
- selama `Zone` dan `E / SL / TP` masih `-`, trader sebaiknya tetap membaca itu sebagai fase scan
- `R / DOL` berarti `Reward / Draw on Liquidity`

## Workflow Praktis

Gunakan indikator dengan urutan ini:

1. Lihat `Bias`
- apakah chart bullish, bearish, atau range

2. Lihat `Sessions`
- sesi apa yang sedang aktif
- previous source apa yang sedang dipakai

3. Cari `ERL` yang disapu
- swing high / low
- EQH / EQL
- session high / low
- PDH / PDL / PWH / PWL bila relevan

4. Lihat `Pref`
- apakah indikator lebih condong ke long, short, atau wait

5. Lihat chart
- apakah ada OB / FVG terpilih
- apakah harga berada di premium atau discount
- apakah ada sweep / MSS

6. Lihat `Exec`
- `Waiting` = belum trade
- `Confirming` = ada setup, tunggu konfirmasi strict
- `Armed` = mulai fokus
- `Active` = entry bisa valid
- `Managed` = kelola trade, bukan cari entry baru

7. Lihat `SL / TP`, `RR / Cf`, dan `Draw`
- pastikan jarak stop dan target masih masuk akal

## Cara Pakai yang Sehat

Gunakan indikator ini untuk:
- menyaring konteks
- memilih zona
- membaca session liquidity
- membantu timing entry

Jangan gunakan indikator ini untuk:
- entry buta tanpa membaca candle
- melawan struktur hanya karena ada satu box
- menganggap semua sinyal pasti entry

## FAQ Singkat

### Kenapa OTE kadang tidak terlihat?

Karena OTE hanya muncul jika sedang ada OTE aktif yang valid.

### Kenapa SL tidak terlihat?

Biasanya karena setup masih `Waiting`, jadi risk cue belum ditampilkan.

### Kenapa sesi terlihat aneh pada jam lokal saya?

Karena engine session memakai `New York time`, sedangkan sumbu chart mengikuti timezone chart TradingView Anda.

### Kenapa previous source / PDH / PDL tidak lagi banyak terlihat di chart?

Karena visual chart sudah disederhanakan:
- session box + extended session H/L menjadi representasi utama liquidity sesi
- level kalender tetap dipakai engine, tetapi tidak memenuhi chart

## Rekomendasi Pemakaian

Untuk desktop:
- profile `Standard`
- dashboard full
- session visual aktif

Untuk layar kecil:
- profile `Compact`
- compact dashboard

Untuk trader ICT yang lebih ketat:
- pilih profile `Strict Confirm`
- gunakan OTE sebagai konfirmasi tambahan, bukan alasan tunggal

## Penutup

Cara paling sehat memakai indikator ini:
- baca struktur dulu
- lalu sesi
- lalu zona
- lalu dashboard
- baru eksekusi

Jika urutannya dibalik, trader biasanya jadi terlalu fokus pada box atau angka dashboard dan kehilangan konteks market.
