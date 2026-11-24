# BOSP PDF Mapper

Aplikasi web lokal untuk memetakan PDF Kertas Kerja BOSP ke template Excel, mengolah BKU/BHP, dan menghasilkan sheet turunan seperti PAJAK, RO, rekap, BA Rekon, LAM BA Rekon, SPTJM, serta export Excel/PDF.

## Status saat ini

> **Dua dokumen resmi, pembagiannya jelas:**
> - `docs/DAFTAR-BUTUH-KEPUTUSAN-2026-09-12.md` — **DOKUMEN TUNGGAL untuk membaca & memutuskan** (keputusan yang berlaku, yang masih hidup, kontradiksi, butir yang menunggu keputusan pemilik (✅ **kini nol baris**), urutan kerja). Buka ini dulu.
> - `BOSP-PROJECT-CANONICAL-PLAN.md` — blueprint teknis (roadmap, status per tahap, current phase/sprint, lock history, evidence, next action).
>
> README ini hanya pengantar teknis dan dapat tertinggal dari keduanya. Dokumen status lama (`PROJECT-STATUS-AKTUAL.md`, `HANDOVER.md`, `STATUS-COMPARE-*.md`) sudah tidak dipakai/dihapus.

Project masih **pilot/fondasi** dan belum public-ready.

Yang sudah kuat:

- Workflow dan tampilan akun sekolah.
- Parser PDF BKU/BHP/ARKAS.
- Editor BKU/BHP/ARKAS.
- Engine sheet turunan.
- Viewer dan export dasar.
- Fondasi login, role, scope wilayah, dan Supabase master.

Yang sudah tersedia dan terbukti pada pilot:

- Persistence BKU/BHP dan model periode/dataset terhubung ke Supabase.
- Gate periode aktif dan workflow `SYNCED → FORWARDED → VERIFIED/LOCKED` tersedia; schema/data workflow sudah terdeteksi di Supabase live.
- Authorization role/scope dan regression test regional lulus (status resmi dan bukti terbaru lihat `BOSP-PROJECT-CANONICAL-PLAN.md`); Golden Test baseline 2026 tersedia di `docs/tests/golden/`, dengan satu mismatch kapitalisasi LAM BA REKON yang masih tercatat sebagai backlog test.

Yang belum final untuk production — ⚠️ **disinkronkan 2026-09-21.** Daftar ini **bukan status resmi**; yang berlaku hanya `docs/DAFTAR-BUTUH-KEPUTUSAN-2026-09-12.md` **B.7**, aturan membacanya di `docs/README.md` **§6.0**:

- Golden test formal terhadap fixture/expected workbook. *(masih ada satu mismatch kapitalisasi `LAM BA REKON` sebagai backlog test)*
- DB-first penuh untuk edit manual, payload runtime, viewer, dan seluruh export. *(belum)*
- Audit transaksi/export menyeluruh. *(belum)*
- ~~Backup otomatis dan restore test.~~ ✅ **SUDAH BERES 2026-09-21** — cadangan otomatis **harian 01:30** benar-benar berjalan (set `20260920183001` dibuat sendiri oleh cron), `verify` PASS, salinan kedua diunduh ke laptop, latihan pulih **12/12 tabel** (2026-09-17). Sisa hanya **retensi** (keputusan 49 / D-c)
- Session persistence, RLS, rate-limit, security headers, upload hardening, dan deployment production — ⚠️ **sebagian sudah beres**: **deploy produksi JALAN** (`https://kertas-kerja.my.id`, HTTPS + HSTS), **unggahan besar terbukti** (30 MB berhasil, nol permintaan `413`), **pemantauan** aktif (cron-job.org + notifikasi email). **Yang masih terbuka:** **session persistence** (B.7 baris 15) dan **RLS** (B.7 baris 14)

### Perapian dokumen & sampah (2026-09-16)

- **`docs/` dirapikan: halaman depan dari 23 berkas menjadi 6.** 17 dokumen historis dipindahkan ke `docs/arsip/` — **tidak ada isi yang dihapus**.
- **Pintu masuk sekarang satu:** `docs/DAFTAR-BUTUH-KEPUTUSAN-2026-09-12.md`. Indeks lama `RANGKUMAN-AUDIT` / `RANGKUMAN-SCOPE` / `RANGKUMAN-RENCANA-KERJA-BERTAHAP` ikut diarsipkan karena hanya menyalin angka dari dokumen sumber.
- **Artefak lokal dibuang ke Recycle Bin.** Hampir semuanya berkas yang belum pernah masuk repo atau sudah ditandai `.gitignore`: `fz1`–`fz9`, `fza`, `fzm.txt` (9 di antaranya identik byte-per-byte), `cov.js`, `gd.txt`, artefak `$null`, log `bosp-serve.log`, dan cache `node-compile-cache/` (551 berkas). **Satu berkas yang ternyata sempat masuk repo juga dibuang:** `image_generate/canvas-delivery-ledger-*.json` — sisa artefak plugin lain, dan **masih bisa dipulihkan dari riwayat git** bila ternyata dibutuhkan.
- **Sengaja TIDAK dibuang:** `backup/` (3 dump SQL), `_private/`, `shots/`, dan 651 berkas bukti mentah di `docs/tests/`.

### Update terakhir (2026-09-14)

- **Jalur unduhan Admin Kecamatan: selesai dan ditutup.** Urutan blok sekolah pada hasil unduhan kini alfabetis mengikuti urutan master yang sama dengan UI, dan template Admin Lengkap terbaru benar-benar terbawa. Commit `f30a2c1` (hanya `server.js` + template + satu guard test). Hasil unduhan akhir diverifikasi pemilik: 11 sheet, BKU sampai baris 31413, PAJAK 5646, RO 16133, BHP 26631. **Scope ini tidak akan diungkit lagi.**
- **Audit transisi viewport 4 role: selesai (read-only).** Keempat role (Sekolah, Admin Kecamatan Purwadadi, Admin Kabupaten Subang, Super Admin) diukur pada urutan turun 1920→375 lalu naik 480→1920. Hasilnya: tidak ada histeresis, kondisi 1920 akhir identik dengan awal, tidak ada overflow horizontal halaman, dan jumlah kolom tidak pernah berubah. BKU memakai `table-layout: fixed` dengan inline `1582px` di keempat role. Laporan: `docs/tests/evidence/audit-transisi-semua-role-2026-09-14/AUDIT-FINAL-4-ROLE.md`.
- **Perbaikan UI NEXT-01…NEXT-06: ada di kode, belum divalidasi pemilik.** Commit `da5cb04` menyentuh `public/app.js` saja; `public/index.html`, `public/style.css`, dan `public/account-settings.css` tidak disentuh. Pemilik baru memeriksa NEXT-02 dan NEXT-03. Tahap validasi berikutnya ada di `docs/arsip/SCOPE-GABUNGAN-UI-RESPONSIF-BERTAHAP-2026-09-14.md`.
- **Tiga dokumen pintu masuk dibuat:** `docs/arsip/RANGKUMAN-AUDIT.md`, `docs/arsip/RANGKUMAN-SCOPE.md`, `docs/arsip/RANGKUMAN-RENCANA-KERJA-BERTAHAP.md`.
- **Catatan tahan-uji:** temuan BKU 12→10 kolom (dari `docs/arsip/AUDIT-TRANSISI-TABEL-2026-09-14.md`) **belum direproduksi** pada audit 4 role 2026-09-14. Jangan dikutip sebagai kondisi sekarang tanpa pengukuran baru.

Status resmi, roadmap, dan next action tetap di `BOSP-PROJECT-CANONICAL-PLAN.md` (§4 dan §7 "JEJAK 2026-09-14").

### ARSIP — Update sebelumnya (2026-09-10)

> Catatan di bawah adalah jejak sejarah. Status aktif project: STEP 0–8 = `DONE 100%`, hanya **STEP 9** yang tersisa.

- **Bug kolom kode jalur riwayat: diperbaiki & terverifikasi browser.** Sebelumnya kolom `KODE KEGIATAN`, `KODE REKENING`, `NOMOR BUKTI`, `ID BARANG`, `HARGA SATUAN`, dan `KdJns` tampil kosong pada jalur riwayat walau API-nya terisi. Kini nilainya identik dengan jalur live (BHP 243/243; BKU 303/306/304 dari 383). Perbaikannya murni di server melalui normalizer tunggal `lib/transaction-rows.js` — **`public/app.js` tidak diubah sama sekali.**
- 🗄️ **ARSIP — Pager sekolah (Bug B):** pernyataan lama "belum diperbaiki" **sudah tidak berlaku**. Kolom pencarian berhenti terisi otomatis, nama sekolah aktif tampil di sebelah judul tabel, dan pager sekolah dipisah ke elemennya sendiri (Fase 3 & 4, commit `01a04ef` dan lanjutannya). Fase 6 (isolasi pilihan sekolah antar tab) **sengaja tidak dikerjakan** sesuai keputusan user.
- 🗄️ **ARSIP — Performa perpindahan tab:** pernyataan lama "belum dikerjakan" **sudah tidak berlaku**. Optimasi Tahap A/B/C.2 terpasang (cache server + cache client + cache engine) dan terukur hangat: BKU 598 ms · PAJAK 846 ms · RO 896 ms · BA REKON 441 ms · LAM 617 ms · SPTJM 347 ms. Sisa temuan lambatnya `/api/sheet/PAJAK` adalah arsip temuan audit, bukan pekerjaan aktif.

Baca `BOSP-PROJECT-CANONICAL-PLAN.md` untuk status resmi, roadmap, dan rencana kerja tahap berikutnya.

## Dokumen project

**Kalau cuma mau memutuskan sesuatu, buka satu berkas saja: `docs/DAFTAR-BUTUH-KEPUTUSAN-2026-09-12.md`.** Tidak ada kalimat di dalamnya yang menyuruh membuka berkas lain.

| Dokumen | Isi |
|---|---|
| `docs/DAFTAR-BUTUH-KEPUTUSAN-2026-09-12.md` | **DOKUMEN TUNGGAL (A–H).** Keputusan yang berlaku, yang masih hidup, kontradiksi, butir yang menunggu keputusan pemilik (✅ **kini nol baris**), urutan kerja |
| `BOSP-PROJECT-CANONICAL-PLAN.md` | Blueprint teknis: roadmap, status per tahap, lock history, changelog |
| `docs/SCOPE-GABUNGAN-BOSP.md` | **Kanal scope resmi.** Scope baru ditambahkan sebagai bagian baru di sini |
| `docs/audit/AUDIT-GABUNGAN.md` | **Kanal audit resmi.** Audit baru ditambahkan di sini |
| `docs/README.md` | Peta isi folder `docs/` |
| `docs/arsip/` | 18 dokumen historis. **Jangan dibaca untuk memutuskan** |

Indeks lama `docs/arsip/RANGKUMAN-AUDIT.md`, `docs/arsip/RANGKUMAN-SCOPE.md`, dan `docs/arsip/RANGKUMAN-RENCANA-KERJA-BERTAHAP.md` **dipindahkan ke `docs/arsip/` pada 2026-09-16** — ketiganya hanya menyalin angka dari dokumen sumber, dan frasa *"masih terbuka"* di dalamnya bukan pekerjaan yang sedang berjalan.

### Aturan dokumentasi yang mengikat

1. **Blueprint wajib diperbarui setiap ada perubahan yang sudah dikerjakan** — pada commit yang sama, bukan menyusul.
2. **Status wajib eksplisit** dan hanya memakai nilai dari Status legend blueprint (§0): `LOCKED`, `DONE 100%`, `IN PROGRESS`, `PENDING VERIFICATION`, `NOT STARTED`, `BLOCKED`. Dilarang menulis status samar seperti "sudah diperbaiki".
3. **Audit baru ditambahkan ke `docs/audit/AUDIT-GABUNGAN.md`**; scope baru ditambahkan ke `docs/SCOPE-GABUNGAN-BOSP.md`.
4. Sebelum commit perubahan pada `server.js` atau `public/app.js`, jalankan `frozen-zone.js --check` dan `--role-audit`.

### Batas role untuk perubahan (LOCKED 2026-09-10)

| Kategori | Berlaku untuk | Cakupan |
|---|---|---|
| **R1 — Performa perpindahan tab** | **SEMUA role** (sekolah + admin) | Cache query server, cache hasil di client, hapus request ganda, guard klik, cache parse template, render bertahap |
| **R2 — UI atau filter** | **HANYA role admin** | Pager sekolah, footer pager, kolom pencarian/pemilih sekolah, elemen kontrol sekolah |

## Cara menjalankan

Persyaratan: Node.js yang mendukung dependency project.

```powershell
cd D:\tes
npm install
npm start
```

Buka:

```text
http://localhost:3000
```

`start.bat` juga dapat digunakan.

### Restart server Windows

Jika port 3000 masih dipakai proses lama:

```powershell
netstat -ano | Select-String ':3000'
taskkill /PID <PID> /F
netstat -ano | Select-String ':3000'
npm start
```

Jangan start server baru sebelum port lama benar-benar bebas.

## Alur akun sekolah saat ini

```text
PDF BKU/BHP
  → parser
  → payload sekolah
  → engine
  → PAJAK / RO / REKAP / BA / LAM / SPTJM
  → browser atau export
```

Data runtime masih memakai kombinasi state browser, cache/store runtime server, dan Excel runtime pada bagian pilot yang belum cutover penuh. Persistence BKU/BHP dan model periode/dataset sudah terhubung ke Supabase; sisa Fase 3B adalah memindahkan edit manual, viewer, payload runtime, dan seluruh export ke sumber DB resmi.

Artefak Fase 3A/3B dan workflow Fase 4 sudah tersedia, committed, dan schema/data utamanya terdeteksi di Supabase live:

- `supabase/phase3a-period-dataset.sql` — model periode, dataset, metadata sumber, dan RPC replace atomik.
- `supabase/phase3b-rpc-fix.sql` — perbaikan RPC persistence BKU/BHP.
- `supabase/phase4-admin-active-period.sql` — periode aktif per scope admin.
- `supabase/phase4-workflow-forwarded.sql` — status `FORWARDED` dan metadata forward.
- `lib/transaction-dataset-contract.js` — validasi periode, tipe/status dataset, transisi status, dan key dataset.
- `lib/supabase.js` — adapter periode/dataset dan query regional.
- `scripts/test-phase3a-model.js` — test model periode/dataset.
- `scripts/test-transaction-adapter.js` — test adapter tanpa credential live.
- `public/periode-resolver.js` — sumber aturan periode resmi dari tanggal BKU+BHP.
- `scripts/test-period-resolver.js` — test Triwulan I-IV, Semester I/II, lintas tahun, dan tanggal invalid.

Migration dan workflow tersebut tidak perlu dijalankan ulang tanpa migration baru dan rencana rollback. Sisa pekerjaan Fase 3B adalah cutover DB-first penuh untuk edit manual, viewer, payload runtime, dan export.

Fondasi quota lifetime juga tersedia secara lokal:

- `supabase/lifetime-quota.sql` — semua akun default FREE, 1 upload + 1 unduh seumur hidup; PRO bypass.
- `lib/lifetime-quota-contract.js` — validasi plan/action/counter.
- `scripts/test-lifetime-quota.js` — test Free/Pro tanpa database live.

Fondasi quota lifetime tersedia di source. Aktivasi enforcement quota tetap keputusan terpisah dan harus dilakukan setelah verifikasi konfigurasi serta test live. Route upload PDF utama tetap mengikuti role dan kontrak regional yang sudah diuji; jangan menyamakan quota dengan status persistence/workflow.

## Menu utama

- **ARKAS** — editor RKAS dengan parser/template terpisah.
- **BKU** — upload/edit Buku Kas Umum.
- **BHP** — upload/edit Barang Habis Pakai.
- **PAJAK** — tabel pajak dari transaksi BKU dan template pajak.
- **REKAP SEMESTER** — rekap hasil engine.
- **RO** — rincian objek belanja per NPSN dan kode rekening.
- **REKAP BELANJA** — agregat pendapatan/belanja/saldo.
- **BA REKON** — dokumen berita acara per sekolah terpilih.
- **LAM BA REKON** — lampiran KdJns/KdRO/NmRO/jumlah/selisih.
- **SPTJM** — surat pernyataan tanggung jawab mutlak.
- **UNDUH** — export Excel/PDF/template lengkap.
- **PENGATURAN** — fungsi master dibatasi berdasarkan role.

## Struktur workbook referensi

Workbook audit `1. REKAPAN Kertas Kerja 2025 Semester I SDN KEC. PURWADADI.xlsx` memiliki 11 sheet:

```text
Daftar Sekolah + Kode Rekening
  → BKU
  → PAJAK / RO / Rekap Semester / Rekap Belanja
  → BA Rekon / LAM BA Rekon / SPTJM
  → BHP
```

Workbook memakai banyak formula `SUMIFS`, `VLOOKUP`, referensi antar-sheet, subtotal, dan formula sisa pajak/saldo. Laporan perbandingan dengan aplikasi ada di `docs/audit/AUDIT-GABUNGAN.md` (bagian `AUDIT-WORKBOOK-VS-ADMIN-KECAMATAN.md`).

## Rencana pengembangan

Urutan kerja yang dipakai:

1. **Fase 3A:** model periode dan dataset.
2. **Fase 3B:** CRUD/import BKU/BHP Supabase per `school_id + periode_id`.
3. **Fase 3C:** golden test engine vs workbook.
4. **Fase 4:** workflow Admin Kecamatan/Kabupaten berbasis transaksi persisten.
5. **Fase 5:** audit log menyeluruh, backup/restore, RLS/security hardening.
6. **Fase 6:** dashboard final dan deployment.

Jangan memperbaiki tab regional secara terpisah sebelum sumber data dan model periode stabil.

## Aturan scope

- Sekolah: hanya sekolah sendiri.
- Admin Kecamatan: SD/SDN Negeri dalam kecamatannya.
- Admin Kabupaten: SD/SDN Negeri dan SMP Negeri dalam kabupatennya.
- Super Admin: fungsi master sesuai kebijakan.

Scope harus ditentukan server dari session/database, bukan dipercaya dari query/body frontend.

## Referensi style ARKAS

Sumber: `docs/analysis/ANALISIS-STYLE-REFERENSI-ARKAS.md`.

Style target yang direncanakan, tetapi belum menjadi pekerjaan aktif:

- Background navy berlapis.
- Surface/card navy dengan depth, bevel, border, dan shadow.
- Royal-blue header tabel dan cyan highlight.
- Tombol biru/hijau glossy, outline navy, tombol destruktif merah.
- Sidebar/topbar navy gradient dengan active nav glow.
- Body tabel dark navy/blue zebra, grid tipis, hover/focus jelas.
- Kategori ARKAS: pink `#F4CCCC`, hijau `#D9EAD3`, kuning `#FFF2CC`.

Skin global direncanakan CSS-only dan tidak boleh mengubah data, API, parser, state, layout, ID, pagination, atau export. Eksekusi skin ditunda sampai pekerjaan data regional stabil.

## API utama

| Method | Endpoint | Fungsi |
|---|---|---|
| POST | `/api/upload` | Upload PDF BKU/BHP |
| POST | `/api/upload-arkas` | Upload PDF ARKAS |
| GET | `/api/auth/me` | Status session |
| GET | `/api/sheets` | Daftar sheet |
| GET | `/api/sheet/:name` | Viewer sheet scoped |
| GET | `/api/admin/region/schools` | Master sekolah sesuai scope admin |
| GET | `/api/admin/region/summary` | Ringkasan wilayah |
| GET | `/api/admin/export-options` | Pilihan mode/cakupan download admin |
| POST | `/api/admin/export-excel` | Export Excel admin berscope |
| POST | `/api/admin/export-pdf` | Export PDF admin berscope |
| POST | `/api/admin/export-full` | Export template lengkap admin berbasis workbook referensi 11 sheet |
| POST | `/api/sheet/:name/save` | Simpan master terbatas Super Admin |
| POST | `/api/export-excel` | Export Excel |
| POST | `/api/export-pdf` | Export PDF |
| POST | `/api/export-full` | Export template lengkap akun sekolah/Super Admin (jalur lama) |
| GET | `/api/dataset-history/group/:groupId` | Isi satu grup dataset riwayat (folder Riwayat Data admin) |
| GET | `/api/admin/region/editor-dataset` | Dataset editor/viewer regional per sekolah atau agregat |
| GET | `/api/review-pdf` | Pratinjau PDF sumber (upload sesi atau file project) — dipakai tombol Review PDF |

## Catatan antarmuka (2026-09-07)

- **Favicon web** mengikuti tema browser/OS (`prefers-color-scheme`), bukan tema aplikasi `bosp-theme`: mode terang → `sample/ref/Web/Icon Web Mode Terang.png`, gelap → `Icon Web Mode Gelap.png` (deklarasi `<link media=...>` di `public/index.html`). User mengonfirmasi kedua icon sudah cocok; `public/favicon-dark.svg` legacy yang tidak dipakai sudah dipindahkan ke Recycle Bin.
- **Panel Notifikasi** di topbar: badge unread + riwayat per scope akun (`bosp-notifications-v1`); notifikasi otomatis saat sinkron/validasi/pemulihan/upload.
- **Review PDF**: tombol Review PDF membuka viewer PDF sumber di tab browser penuh dari upload sesi atau file project; tidak memakai modal/iframe.
- **Tampilan Cetak**: dokumen BA REKON, LAM BA REKON, dan SPTJM punya tombol Tampilan Cetak yang membuka tab penuh; dokumen F4 bisa digeser pada mobile dan kembali full-width saat dicetak.

## Test dasar

Syntax check:

```powershell
node --check server.js
node --check public/app.js
node --check lib/supabase.js
node --check lib/transaction-rows.js
```

Jumlah skrip test per 2026-09-14: **61** di `scripts/test-*.js` (`test-supabase-rest-pagination.js` ditambahkan bersama perbaikan pemotongan 1000 baris). Status kelulusan suite penuh dicatat di `BOSP-PROJECT-CANONICAL-PLAN.md` §8 — README ini tidak menyimpan angka suite agar tidak basi.

```powershell
Get-ChildItem scripts -Filter "test-*.js" | ForEach-Object { node $_.FullName }
```

Dua guard yang ditambahkan pada 2026-09-14 dan sudah PASS:

```powershell
node scripts/test-admin-export-order-contract.js   # urutan alfabetis hasil unduhan admin
node scripts/test-next-ui-admin-contract.js        # NEXT-01..NEXT-06 admin UI
```

Catatan riwayat: `test-stage1-ro-aggregate-contract` pernah dicatat gagal sebagai backlog, tetapi **sudah LULUS** sejak 2026-09-12 (exit code 0). Catatan itu ditutup dan tidak lagi dihitung sebagai pekerjaan tertinggal.

### Penjaga zona beku & batas role

Wajib dijalankan sebelum commit perubahan pada `server.js` atau `public/app.js`:

```powershell
node docs/tests/tools/frozen-zone.js --check        # zona beku harus utuh
node docs/tests/tools/frozen-zone.js --role-audit   # tidak ada UI/filter tanpa gate admin
node docs/tests/tools/frozen-zone.js --school-guard # daftar hunk untuk review manual
```

Zona beku mencakup 19 file + 5 region kritis: definisi kolom tabel, fungsi render sel, dan seluruh jalur download/export. Baseline hash ada di `docs/tests/tools/frozen-zone-baseline.json`. Untuk merekam ulang baseline setelah perubahan yang disengaja: `--init`.

E2E authenticated memerlukan credential uji melalui environment variable, bukan source code:

```powershell
$env:E2E_USERNAME='akun-uji'
$env:E2E_PASSWORD='password-uji'
node scripts/e2e-test.js
```

## Keamanan dan batasan

- Jangan commit `.env`, token OAuth, service key, password, atau credential.
- `template/current.xlsx` adalah file kerja lokal, bukan database transaksi multi-sekolah.
- Jangan mengubah data produksi untuk pengujian tanpa permintaan eksplisit.
- Jangan menghapus file secara permanen; gunakan Recycle Bin.
- Project belum public-ready sampai persistence, scope, formula test, audit, backup, dan hardening selesai.
