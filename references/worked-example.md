# Worked Example

A complete Standard-profile session, compressed. Shows wave shape, ledger recaps, a conflict, and closure.

---

**User:** uji ide — mau bikin tool buat rekonsiliasi CSV mingguan dari bank

**Agent:**

```
Profile: Standard (inferred — "mau bikin tool", scope belum jelas).
Override: quick / deep / exhaustive.

Root question first, because everything hangs off it.

🌊 Wave 1 — 3 open decisions
Frontier: N0, N1, N2

**Q1 · N0 — Is a tool the answer?**  `importance 5 · one-way-door`
Rekonsiliasi mingguan bisa berarti: (a) tool kecil yang Anda pakai sendiri,
(b) proses manual + template Excel yang lebih rapi, (c) bagian dari sistem
akuntansi yang sudah ada. (b) sering menang kalau volumenya < 50 baris/minggu
dan tidak ada yang perlu diaudit.

→ Recommended: (a) hanya jika > 100 baris/minggu atau butuh jejak audit.
else (b).

---

**Q2 · N1 — Siapa yang memakai?**  `importance 4 · hard`
Satu orang, atau beberapa orang yang harus melihat hasil sama? Ini menentukan
apakah state perlu dishare.

→ Recommended: satu akuntan, tanpa login — paling sederhana.

---

**Q3 · N2 — Apa definisi "cocok"?**  `importance 5 · one-way-door`
Exact match per transaksi, atau toleransi selisih (mis. ±Rp1 untuk pembulatan),
atau match berbasis tanggal+nominal saja? Ini menentukan seluruh logika.

→ Recommended: exact per (tanggal, nominal, referensi); toleransi ditambahkan
belakangan sebagai flag, bukan default.

Answers: reply per number, or "ok" to accept all.
```

**User:** `1 ok, 2 dua orang (saya + partner), 3 exact, tapi kadang bank ubah format tanggal`

**Agent:**

```
📋 Settled (3) · N0 Tool kecil (bukan proses manual). N1 Dua pengguna.
N2 Exact match per (tanggal, nominal, referensi).
⚠️ Baru: format tanggal bank tidak stabil → cabang baru.

🌊 Wave 2 — 5 open decisions
Frontier: N3, N4, N6, N7, N8   Blocked: N5 (on N6)

**Q3 · N3 — Dua pengguna, bagaimana berbagi state?**  `importance 4 · hard`
(a) dua orang pakai file lokal masing-masing, (b) satu folder bersama
(Dropbox/Drive), (c) satu instance kecil dengan database.

→ Recommended: (b) folder bersama — tanpa server, tanpa login, cukup untuk 2 orang.

---

**Q4 · N6 — Format tanggal tidak stabil: normalisasi di mana?**  `importance 5 · hard`
(a) pilih format manual tiap kali impor, (b) auto-detect per kolom dan tampilkan
hasil deteksi untuk dikonfirmasi, (c) hanya dukung satu format dan tolak sisanya.

→ Recommended: (b) — auto-detect + konfirmasi, karena (a) cepat membosankan
dan (c) akan gagal bulan depan.

---

**Q5 · N4 — Apa yang terjadi kalau ada baris bank tanpa pasangan?**  `importance 4 · easy`
Diamkan, tandai kuning, atau gagal seluruh proses?

→ Recommended: tandai, jangan gagal — satu baris aneh tidak boleh memblokir minggu itu.

---

**Q6 · N7 — Output-nya ke mana?**  `importance 3 · easy`
XLSX baru, sheet tambahan di file input, atau keduanya?

→ Recommended: XLSX baru di folder yang sama; input tidak pernah dimodifikasi.

---

**Q7 · N8 — Kalau satu pengguna sedang menjalankan, yang lain buka?**  `importance 3 · hard`
Abaikan, lock file, atau last-write-wins pada output?

→ Recommended: file output bertimestamp, tidak pernah overwrite — konflik hilang
tanpa lock.

Answers: reply per number, or "ok".
```

**User:** `ok semua, tapi N3: partner pakai Windows, saya Linux`

**Agent:**

```
📋 Settled (7) · N0 tool kecil · N1 2 user (Linux + Windows) · N2 exact match
· N3 folder bersama · N4 tandai tanpa gagal · N6 auto-detect tanggal
· N7 XLSX baru · N8 output bertimestamp

🌊 Wave 3 — Red team — 4 open decisions
Frontier: N10, N11, N12, N13

**Q8 · N10 — Linux + Windows: apa yang paling mungkin pecah?**  `importance 4 · hard`
Path separator, encoding CSV (BOM dari Excel Windows), newline, dan case-sensitivity
nama file. Mana yang perlu ditangani eksplisit?

→ Recommended: semua empat — encoding BOM adalah penyebab #1 file "rusak" antar-OS.

---

**Q9 · N11 — Tabrakan timestamp: dua output dibuat di menit yang sama?**  `importance 4 · easy`
Detik, atau suffix acak, atau cek dan tambah counter?

→ Recommended: timestamp sampai detik + counter kalau sudah ada.

---

**Q10 · N12 — File bank 3 bulan mendadak berubah kolom. Apa yang user lihat?**  `importance 3 · hard`
Error teknis, atau laporan "kolom X tidak dikenali, ini yang saya temukan"?

→ Recommended: laporan kolom + minta konfirmasi mapping, simpan mapping itu.

---

**Q11 · N13 — Apakah rekonsiliasi lama perlu bisa dibuka ulang?**  `importance 2 · easy`
Kalau ya, output harus menyimpan input aslinya.

→ Recommended: tidak — output bertimestamp sudah cukup sebagai arsip.

Answers: reply per number, or "ok".
```

**User:** `ok. satu lagi: kalau ternyata nanti perlu 5 orang, apakah desain ini bisa naik?`

**Agent:**

```
⚠️ Conflict check — N1 "dua pengguna, folder bersama" vs pertanyaan Anda tentang 5 pengguna.
Keduanya bisa benar hanya kalau keputusan berbagi state mudah ditingkatkan.
Itu bukan asumsi yang boleh dibiarkan.

🌊 Wave 4 — 2 open decisions
Frontier: N14, N15

**Q12 · N14 — Folder bersama harus bisa naik ke 5 pengguna?**  `importance 4 · one-way-door`
(a) Ya, dan kita desain sekarang dengan satu file "state" yang bisa dipindah ke
server nanti, (b) Tidak — 5 pengguna akan jadi tool berbeda, (c) Ya, tapi cukup
secara konsep (bukan implementasi).

→ Recommended: (a) — biayanya kecil sekarang (satu file state, bukan state
tersebar di banyak file), dan menghindari penulisan ulang total.

---

**Q13 · N15 — Kalau naik ke server, siapa yang mengelolanya?**  `importance 3 · hard`
Anda sendiri, partner, atau tidak ada (hosted)?

→ Recommended: tidak ada → berarti opsi (b) di N14 lebih realistis kalau tidak
ada yang mau jadi admin.

Answers: reply per number, or "ok".
```

**User:** `12: a. 13: tidak ada yang mau jadi admin.`

**Agent:**

```
Both answers accepted, but they point opposite ways — noted as a tension, resolved by
your call: N14 = (a) design for upgrade, N15 = no admin → the upgrade target is a
managed/hosted shape, not self-hosted. No further question needed.

📋 Settled (12) — full ledger
✅ Closure check (Standard)
- Tree exhausted: 15 nodes, 4 waves ✓
- No open parents ✓
- Deferred (2): N16 column-mapping UI polish; N17 retry-on-locked-file — implementer's call
- "Don't know": none outstanding
- Contradictions: 1 found (N14/N15), resolved by user ✓
- Ungrillable: none

Accepted risks: none.
Open one-way-doors: N14 (upgrade path) — confidence: user stated.

I understand this as: "A small two-person tool that matches bank CSV rows to internal
records by (date, amount, reference), normalising unstable date formats with confirmation,
run from a shared folder by a Linux and a Windows user, producing a new timestamped XLSX
each run without ever modifying the input — and structured so state can move to a hosted
service if the team grows."

Does that match what you have in your head?
```

---

## What this example demonstrates

| Behaviour | Where |
|---|---|
| Root question before mechanics | Q1 |
| Profile stated and overridable | Wave 1 header |
| Whole frontier per wave, with blocked list | Wave 2, 3 |
| Recommendation + reason on every question | All |
| Importance/reversibility tags | All |
| Ledger recap before each wave | Wave 2, 3, 4 |
| New branch opened by an answer | N6 (unstable dates) |
| Red-team wave rather than more extension | Wave 3 |
| Conflict surfaced instead of silently resolved | N14/N15 |
| Deferrals made explicit, not dropped | Closure |
| Closure evidenced, then confirmation requested | End |
