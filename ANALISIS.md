# 📋 Analisis Teknis & Keamanan — Google_AI_Pro_2Year_ZeroCost_Auto

> **Peringatan & batasan dokumen**
> Dokumen ini adalah analisis independen atas kode yang beredar di komunitas, disusun **murni untuk tujuan edukasi dan kesadaran keamanan**. Dokumen ini BUKAN panduan penggunaan, tidak berafiliasi dengan pembuat alat, dan sengaja tidak memuat langkah operasional, token, atau potongan kode yang dapat langsung dipakai. Penyalahgunaan promo langganan melanggar Ketentuan Layanan Google dan dapat berujung sanksi akun hingga konsekuensi hukum.

---

## 1. Ringkasan Eksekutif

`Google_AI_Pro_2Year_ZeroCost_Auto` adalah **konsol otomasi berbasis browser** yang dirancang untuk mengklaim langganan **Google AI Pro (5 TB)** secara **gratis hingga 24 bulan per akun**, dan memproses **banyak akun Google sekaligus secara massal** (istilah dalam kodenya: 批量放号 / 跑号 — jargon *account farming*).

Kesimpulan analisis: alat ini adalah **perangkat penyalahgunaan promo (promo abuse)**, bukan eksploitasi keamanan Google dan bukan promo resmi. Ia bekerja dengan:

1. Memakai **token penawaran (offer token) Google One** yang diambil dari jalur distribusi tak resmi dan dipaksakan berlaku universal untuk akun mana pun;
2. **Memanipulasi kondisi halaman checkout** `one.google.com` lewat injeksi JavaScript agar tawaran US$0.00 muncul dan lolos dari pemeriksaan kelayakan;
3. **Menumpuk tiga lapis paket gratis** (12 + 6 + 6 bulan) per akun;
4. **Menyamarkan lokasi** melalui proxy residensial Amerika Serikat;
5. Membatalkan auto-renew di akhir agar tidak tertagih.

---

## 2. Asal-Usul

- Upstream repo asli: `worldonlyu/Google_AI_Pro_2Year_ZeroCost_Auto`. Pemilik repo **menyangkal sebagai pengembang** ("bukan proyek saya, hanya dibagikan untuk analisis") — pola umum alat yang bocor/beredar di komunitas pasar gelap.
- README mengarah ke toko jual kartu/lisensi (`faka.cdkey.si`) dan grup Telegram — indikasi kuat ekosistem grey-market.
- Seluruh dokumentasi asli berbahasa Mandarin, sasaran penggunanya komunitas pencari keuntungan promo lintas akun.

---

## 3. Arsitektur Teknis

**Stack:** Python 3.10+ · FastAPI + uvicorn (konsol web lokal) · Playwright (kendali Chrome sungguhan) · pydantic · proxy residensial rotasi.

| Berkas | Peran |
|---|---|
| `app.py` | Web console di `http://127.0.0.1:8765` — form input kartu, impor akun massal, tombol eksekusi, log, galeri screenshot |
| `automation_engine.py` (±96 KB) | Inti otomasi: kelas `GoogleUltraAutomator` dengan pipeline 5 langkah, jembatan proxy lokal, kalkulator TOTP |
| `config.json` | Konfigurasi — **menyimpan data kartu (PAN, CVC, kedaluwarsa) dan daftar kredensial akun dalam teks polos (plaintext)** |
| `templates/` | Antarmuka web konsol |
| `1_…bat`, `2_…bat` | Instal dependensi & launcher harian |

Direktori kerja: `profiles/` (satu profil Chrome per akun) dan `screenshots/` (bukti jalannya proses).

---

## 4. Cara Kerja Engine (Konseptual)

1. **Token penawaran hard-coded.** Kode menyimpan banyak offer token Google One (paket 12 bulan, dua varian 6 bulan, paket bulanan, hingga token layanan video) dengan komentar eksplisit *“universal across accounts/currencies”* — artinya token promo yang seharusnya hanya untuk segmen pengguna tertentu dipaksa dipakai ke semua akun.
2. **Injeksi & patch halaman (`HOOK_JS`).** Script menyuntik JavaScript ke `one.google.com`, lalu memodifikasi struktur data internal halaman (array bersarang pada state checkout) agar penawaran US$0.00 terpilih — **melewati validasi kelayakan** yang seharusnya dijalankan server.
3. **Pipeline per akun** (`run_pipeline`):
   - `step1_login` — login otomatis; mendukung email–password–email pemulihan, serta **menghitung kode 2FA sendiri dari seed TOTP** yang diimpor pengguna;
   - `step2_layer1_12m` — tempel kartu (alamat Portland, Oregon `97201`, negara bagian bebas pajak penjualan) + klaim paket 12 bulan;
   - `step3_layer2_6mss` / `step4_layer3_6m` — tumpuk dua paket 6 bulan → total 24 bulan;
   - `step5_cancel_and_verify` — buka Google Play, **batalkan auto-renew**, ambil screenshot bukti;
   - `verify_ip_environment` — cek IP keluar via layanan `ipinfo.io`/`ipwho.is` untuk memastikan tampak sebagai AS.
4. **Pagar “US$0.00 only”.** Engine hanya menekan tombol subscribe bila modal checkout menampilkan US$0.00 — mencegah tertagih, tetapi **tidak mengubah sifat abusenya**.
5. **Proxy per akun.** Kelas `LocalHttpProxyAuthBridge` menyambung ke layanan proxy residensial rotasi pihak ketiga (`floppydata.net`, `geo.g-w.info`) — tiap akun dapat identitas IP residensial AS berbeda.
6. **Mode batch.** Format impor akun `email----password----email-pemulihan----seed-2FA` per baris; antarmuka menyediakan tombol eksekusi massal dan per-akun.

---

## 5. Mengapa Ini Pelanggaran

| Tindakan alat | Ketentuan yang dilanggar |
|---|---|
| Menumpuk tiga lapis paket gratis per akun | Batas satu promo per akun pada ketentuan promo Google One |
| Memproses akun secara massal | Larangan multi-akun untuk mengakali batas layanan |
| Otomatisasi akses akun & checkout | Larangan akses memakai metode otomatis tanpa izin |
| Proxy residensial agar tampak pengguna AS | Penyelubungan lokasi / syarat kelayakan region |
| Menggunakan token offer di luar penerima berhak | Penyalahgunaan mekanisme promosi |

Kombinasi ini umumnya memenuhi unsur **memperoleh layanan berbayar melalui manipulasi** — dalam banyak yurisdiksi dapat dikategorikan ketidakwajaran/fraud. Nilai klik “US$0.00” tidak mengubah status tersebut.

---

## 6. Risiko bagi Pengguna

| Risiko | Dampak nyata |
|---|---|
| **Banned akun & clawback** | Google dapat mematikan langganan, menonaktifkan akun (termasuk Gmail/Drive yang melekat), dan **menagih penuh** ke kartu yang ditempel |
| **Paparan kredensial total** | Pengguna mengetik password + email pemulihan + **seed 2FA** + **nomor kartu + CVC** ke dalam script anonimen; `config.json` menyimpan semuanya **plaintext** di disk |
| **Tak dapat diaudit** | Engine ±96 KB dari sumber anonim terhubung toko kartu gelap; pemindaian cepat tidak menemukan eksfiltrasi kartu, tetapi itu **bukan jaminan** — versi baru bisa berubah kapan saja |
| **Eksposur hukum** | Distribusi/penggunaan alat fraud berpotensi masalah hukum bagi pengguna maupun yang menyebarkan |
| **Reputasi akun GitHub** | Fork publik alat semacam ini menempel di profil |

## 7. Catatan Keamanan Teknis (temuan pindaian)

- Endpoint jaringan yang teramati di kode: domain Google (`accounts/one/play/payments.myaccount`), layanan cek IP (`ipinfo.io`, `ipwho.is`), dan layanan proxy pihak ketiga (`floppydata.net`, `geo.g-w.info`).
- Tidak ditemukan pengiriman data kartu ke domain non-Google pada versi yang diperiksa — namun **kesimpulan “aman” tidak bisa diberikan** untuk binary/script tertutup dari sumber anonim.
- Kredensial tersimpan tanpa enkripsi; siapa pun (atau malware apa pun) yang membaca berkas itu memegang kendali penuh akun.

---

## 8. Kesimpulan & Rekomendasi

**Kesimpulan:** ini alat *promo abuse* berbasis browser automation untuk farming langganan gratis lintas akun — bukan celah keamanan Google, bukan promo resmi, dan bukan “trik aman”. Risiko kerugian (akun, kartu, hukum) melampaui nilai manfaatnya.

**Rekomendasi:**

1. **Jangan menjalankan** alat ini dengan data kartu atau akun asli.
2. Jika pernah menjalankan: segera ganti password akun Google terkait, **regenerasi seed 2FA**, dan **blokir/ganti kartu** yang pernah dimasukkan.
3. Pertimbangkan menghapus fork publik dari profil.
4. Untuk kebutuhan AI secara resmi, gunakan jalur legal: tier gratis Google AI Studio / Gemini API, trial resmi, atau paket edukasi.

---

*Dokumen analisis — disusun 2026-11-07. Analisis bersumber dari pembacaan README, `config.json`, `app.py`, dan `automation_engine.py` pada repo upstream. Tidak ada bagian kode atau token berbahaya yang direproduksi di dokumen ini.*
