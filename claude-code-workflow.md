# Panduan Workflow Claude Code

Dokumen ini berisi alur kerja standar saat menggunakan Claude Code, supaya hasilnya konsisten, terarah, dan tidak melenceng dari rencana. Bisa dipakai sebagai referensi pribadi, atau ditempel sebagian isinya ke `CLAUDE.md` project.

---

## 0. Prinsip Dasar

- **Jangan langsung minta implementasi** untuk task yang kompleks/berisiko (auth, database, billing, API publik, arsitektur).
- **Task kecil boleh langsung dikerjakan** tanpa plan (fix typo, rename variable, tambah log).
- **Review terjadi terus-menerus**, bukan cuma di akhir — cek tiap tahap, jangan tunggu semua "selesai" baru diperiksa.

---

## 1. Context (Beri Konteks)

Sebelum minta apa pun, pastikan Claude paham situasi saat ini.

**Yang perlu disampaikan:**
- Bagian codebase mana yang relevan (folder/file)
- Bagaimana sistem terkait bekerja saat ini
- Constraint yang ada (library yang dipakai, konvensi tim, hal yang tidak boleh diubah)

**Contoh prompt:**
```
Baca folder /src/auth dan jelaskan bagaimana session & login
saat ini bekerja. Juga cek bagaimana environment variable
untuk secret dikelola.
```

> Gunakan **Plan Mode** (`Shift+Tab` dua kali, atau `/plan`) di tahap ini, supaya Claude hanya membaca, tidak mengubah apa pun.

---

## 2. Goal (Tentukan Tujuan)

Sebutkan dengan jelas **apa hasil akhir yang diinginkan**, bukan langkah-langkahnya. Biarkan Claude yang menyusun caranya.

**Ciri goal yang baik:**
- Spesifik dan terukur (ada definisi "selesai")
- Menyebutkan batasan (waktu, kompatibilitas, dependency yang boleh/tidak boleh ditambah)

**Contoh prompt:**
```
Saya ingin menambahkan Google OAuth ke aplikasi ini.
File apa saja yang perlu diubah? Bagaimana alur session-nya?
```

---

## 3. Plan (Buat Rencana)

Minta Claude membuat rencana implementasi detail **sebelum** menyentuh kode.

**Yang harus ada di dalam plan:**
- Daftar file yang akan diubah/ditambah
- Urutan pengerjaan
- Potensi risiko / hal yang bisa rusak (breaking change)
- Cara verifikasi (test apa yang akan dijalankan)

**Contoh prompt:**
```
Buatkan rencana implementasi detail untuk goal di atas.
```

**Tips:**
- Tekan `Ctrl+G` untuk membuka plan di text editor dan edit langsung sebelum lanjut.
- Untuk task besar, minta pecah jadi beberapa step kecil, bukan satu plan raksasa.

---

## 4. Implement (Eksekusi + Review Berkelanjutan)

Keluar dari Plan Mode, baru Claude boleh mengubah file.

**Selama tahap ini:**
- Jalankan **per step**, jangan biarkan semua step jalan tanpa jeda untuk task besar
- Minta bukti konkret, bukan klaim: `"tunjukkan hasil test-nya"`, bukan `"apakah sudah jalan?"`
- Bandingkan hasil dengan plan — kalau menyimpang, koreksi saat itu juga

**Contoh prompt:**
```
Implementasikan step 1 dari plan (setup OAuth client).
Setelah itu berhenti dan tunjukkan diff-nya sebelum lanjut ke step 2.
```

```
Tulis test untuk callback handler, jalankan test suite,
dan perbaiki kalau ada yang gagal.
```

> Review di sini **bukan fase terpisah** — dia menyatu di dalam siklus implement: kode → cek → perbaiki → cek lagi, sampai sesuai plan dan test lolos.

---

## 5. Commit

Setelah implementasi selesai dan sudah diverifikasi:

**Contoh prompt:**
```
Commit perubahan ini dengan pesan yang deskriptif,
lalu buatkan PR dengan ringkasan perubahan.
```

Kalau kerja dalam tim, PR ini menjadi gerbang review manusia terakhir sebelum merge.

---

## Ringkasan Alur

```
CONTEXT  →  GOAL  →  PLAN  →  IMPLEMENT (+ review berulang)  →  COMMIT
(baca)      (mau     (rencana   (eksekusi bertahap,              (commit +
             apa?)    detail)    verifikasi tiap step)             PR)
```

---

## Kapan Boleh Skip Plan Mode

| Situasi | Perlu Plan? |
|---|---|
| Fix typo, rename variable, tambah log line | Tidak, langsung minta kerjakan |
| Perubahan menyentuh auth/database/billing/API publik | Ya, wajib plan dulu |
| Refactor besar / ubah arsitektur | Ya, wajib plan dulu |
| Task yang scope-nya jelas & kecil | Tidak, langsung kerjakan |
| Task ambigu / kamu sendiri belum yakin caranya | Ya, plan dulu untuk klarifikasi |

---

## Opsional: Tambahkan ke CLAUDE.md

Supaya aturan ini otomatis diikuti tiap sesi, tempel bagian ini ke `CLAUDE.md` di root project:

```markdown
## Aturan Workflow
- Plan dulu sebelum ubah database, auth, billing, atau API publik.
- Sebutkan file yang terdampak dan risiko kompatibilitas dalam plan.
- Untuk task besar, kerjakan per step dan tunjukkan diff sebelum lanjut.
- Selalu jalankan test dan tunjukkan hasilnya, jangan hanya klaim "sudah jalan".
```
