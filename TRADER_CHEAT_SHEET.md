# Trader Cheat Sheet

Panduan satu halaman yang ringkas, visual, dan siap dibagikan untuk trader `Advanced SMC / ICT Indicator`.

---

## Snapshot Cepat

| Kebutuhan | Pakai |
|---|---|
| Desktop normal | `Standard` |
| Layar kecil / cepat | `Compact` |
| Validasi paling ketat | `Strict Confirm` |

`Strict Confirm` sekarang tidak lagi memaksa `Focused`.
Anda bebas memilih:
- `Strict + Full`
- `Strict + Focused`
- `Strict + Compact dashboard`

| Engine | Nilai |
|---|---|
| Gaya trading | `Intraday ICT` |
| Timeframe entry | `M1`, `M5`, `M15`, `M30` |
| Di luar TF itu | indikator berhenti |

> Indikator ini bukan untuk semua timeframe. Fokusnya adalah `intraday setup engine`.

---

## Workflow 30 Detik

```text
BIAS -> SESSION -> ERL SWEEP -> FVG / POI -> DOL -> EXEC
```

Jika salah satu dari tiga hal ini belum jelas, `wait`:
- arah
- sesi
- execution state

---

## Peran Tiap Timeframe

| TF | Peran | HTF Pair | Profile Cocok | Gaya Pakai |
|---|---|---|---|---|
| `M1` | Precision entry | `15M / 1H` | `Compact` atau `Strict Confirm` | Pakai setelah arah sudah jelas dari `M5/M15` |
| `M5` | Entry utama | `15M / 4H` | `Standard` atau `Strict Confirm` | Chart eksekusi paling seimbang |
| `M15` | Setup / scan utama | `1H / 4H` | `Standard` | Scan struktur dan kualitas setup |
| `M30` | Planning chart | `4H / 1D` | `Standard` | Untuk directional plan, bukan trigger cepat |

**Urutan pakai paling sehat**
- `M15` untuk scan
- `M5` untuk eksekusi utama
- `M1` hanya untuk presisi

---

## Arti State Exec

| State | Arti | Aksi Trader |
|---|---|---|
| `Waiting` | belum ada alasan entry | jangan entry |
| `Confirming` | setup ada, konfirmasi strict belum lengkap | tunggu rapih |
| `Armed` | mulai siap | fokus retest / trigger |
| `Active` | entry sedang valid / sangat dekat | entry boleh dipertimbangkan |
| `Managed` | trade sudah berjalan | kelola trade |
| `Completed` | setup selesai | cari setup berikutnya |
| `Invalidated` | setup batal | abaikan |
| `Expired` | setup basi | abaikan |

**Ringkas**
- `Waiting` / `Confirming` = belum trade
- `Armed` = siaga
- `Active` = boleh entry
- `Managed` = manage, bukan entry baru
- `Completed` / `Invalidated` / `Expired` = lanjut scan

---

## Cara Baca Dashboard

| Field | Fokus Baca | Makna Cepat |
|---|---|---|
| `Bias` | `chart / context / narrative` | kalau konflik, jangan agresif |
| `Sess/Cfm` | sesi aktif + status konfirmasi | tahu market sedang di jam apa dan setup sudah rapi atau belum |
| `Pref` | `Long / Short / Wait` | arah yang lebih layak dipantau |
| `Z / E` | `POI` dan entry | lokasi setup |
| `SL / TP` | stop dan target | risk plan |
| `RR / Cf` | reward/risk dan confidence | pembanding, bukan alasan tunggal |
| `Draw` | `DOL` utama | arah tarikan external liquidity utama |
| `Next` | aksi berikutnya | baca ini paling akhir |

### Baca `Setup Grade`

| Tag | Arti |
|---|---|
| `Q1` | kualitas dasar |
| `Q2` | kualitas menengah |
| `Q3` | kualitas terbaik |

`Q` = `Setup Grade`, dibentuk dari snapshot kualitas setup:
- response / confirm pada POI terpilih
- displacement
- ATR expansion

### Baca `Sess/Cfm`

| Teks | Arti |
|---|---|
| `A-ICT LDN>AS | NY TZ` | model auto ICT, sekarang London, source Asia, jam acuan New York |
| `Standard` | tanpa strict confirmation |
| `Refine` | context belum cukup rapi |
| `KZ Wait` | context sudah baik, menunggu timing killzone |
| `Ready` | konfirmasi strict lengkap |

### Baca `POI` dan `DOL`

| Istilah | Arti Cepat |
|---|---|
| `POI` | `Point of Interest`, zona yang sedang dipakai model |
| `FVG` | POI utama yang diprioritaskan indikator untuk flow ICT |
| `OB` | fallback POI jika tidak ada FVG yang lebih layak |
| `iFVG` | `Inversion FVG`, FVG gagal yang flip fungsi dan dipakai sebagai konteks tambahan |
| `DOL` | `Draw on Liquidity`, target external liquidity utama |

Flow sehat indikator dalam `Strict Confirm`:
- `ERL sweep -> FVG / POI -> DOL`

Flow strict yang lebih eksplisit:
- `ERL sweep -> displacement / MSS -> PD / OTE -> DOL -> killzone`

Catatan:
- `volume tinggi` tidak dipakai sebagai syarat wajib
- indikator sengaja tidak memakai gate volume keras agar tetap robust lintas feed

### Baca `RR / Cf`

- `RR` = reward / risk
- `Cf` = confidence
- jika compact menulis `Ctx L/S`, itu masih `context strength`, belum setup siap entry
<<<<<<< HEAD
- jika compact menulis setup seperti `Bullish OB | Q2 | Ctx`, itu berarti setup kandidat sudah ada tetapi masih `context-only`
- jika compact menulis `Ctx+i`, itu berarti ada `iFVG` aktif yang sedang menambah konteks
- jika compact menulis `Ctx+i+`, itu berarti `iFVG` aktif itu juga selaras dengan setup kandidat
- jika sisi dashboard full sudah `Retired`, maka `Sess/Cfm` sisi itu juga ikut `Retired`
=======
- jika compact menulis setup seperti `Bullish OB | Q2 | Ctx`, itu berarti setup kandidat dengan `Setup Grade Q2` sudah ada tetapi masih `context-only`
>>>>>>> 7157f07044b3a3b41f3b982a9e6c480702bf60a5
- selama `Zone` dan `E / SL / TP` masih `-`, trader tetap membaca kondisi itu sebagai `scan`, bukan entry

### Baca `Next`

Contoh arti cepat:
- `Wait confirm` = belum entry
- `Monitor retest` = setup ada, tunggu sentuh ulang
- `Entry active` = setup sudah siap
- `Manage trade` = jangan buka ulang
- `Wait next setup` = setup lama selesai

---

## Cara Baca Chart

Urutan fokus chart:
1. struktur `HH/HL/LH/LL`
2. `BOS / MSS`
3. `ERL` yang disapu
4. session box + garis `High/Low`
5. zona `OB / FVG` terpilih
6. protected level
7. OTE aktif jika ada

**Prinsip sederhana**
- session = konteks
- `ERL` = sumber liquidity
- `FVG / POI` = lokasi entry
- `DOL` = external target
- `Exec` = timing

---

## Aturan Praktis

### Lakukan

- utamakan setup yang searah `Bias`
- hormati `Sess/Cfm`
- tunggu `Armed` atau `Active` untuk entry
- gunakan `M5` sebagai chart utama bila bingung
- pakai `M1` hanya untuk presisi

### Hindari

- entry hanya karena ada box zona
- memaksa trade saat `Wait`
- menganggap `Cf` tinggi sebagai sinyal tunggal
- memakai `M30` sebagai trigger cepat
- membaca dashboard sebelum membaca struktur

---

## Kombinasi Paling Aman

| Tujuan | Kombinasi |
|---|---|
| Scan arah | `M15 + Standard` |
| Entry utama | `M5 + Standard` atau `Strict Confirm` |
| Precision entry | `M1 + Strict Confirm` |

---

## Jika Bingung

Gunakan checklist ini:

```text
1. Bias jelas?
2. Session jelas?
3. Pref bukan Wait?
4. Exec sudah Armed / Active?
5. Zona terlihat?
6. SL / TP masuk akal?
```

Kalau jawabannya `tidak` pada salah satu poin penting di atas, keputusan terbaik biasanya:

```text
WAIT
```

---

## Bottom Line

Indikator ini paling enak dipakai seperti ini:

```text
Scan di M15 -> Eksekusi di M5 -> Presisi di M1 bila perlu
```

Jangan mulai dari angka dashboard. Mulailah dari:

```text
STRUKTUR -> SESSION -> ZONA -> EXECUTION
```
