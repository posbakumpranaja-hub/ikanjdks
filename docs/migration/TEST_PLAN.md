# TEST_PLAN

Rencana uji regresi dan E2E untuk memastikan migrasi tidak memutus fitur lama.

## 1) Golden tests (old vs new output)

- Tujuan: memastikan output legacy dan output jalur baru setara.
- Skenario minimum:
  - Kasus normal
  - Kasus edge input
  - Kasus invalid input
- Kriteria lulus:
  - Output parity = Match
  - Perbedaan non-fungsional (mis. timestamp) terdokumentasi

## 2) Contract tests

- Tujuan: menjamin kontrak I/O (schema, kode status, field error) tidak berubah.
- Cakupan:
  - request schema
  - response schema
  - error schema
- Kriteria lulus:
  - Seluruh endpoint/entrypoint prioritas lolos validasi kontrak

## 3) Integration tests

- Tujuan: memverifikasi integrasi orchestrator, adapter, worker, dan panel.
- Cakupan:
  - run action
  - cek status
  - cek log/hasil
- Kriteria lulus:
  - Alur end-to-end sukses tanpa fallback paksa

## 4) Failure-mode tests

- Tujuan: memastikan sistem tahan gangguan.
- Skenario minimum:
  - timeout dependency
  - dependency down
  - retry policy
  - error propagation ke panel/API
- Kriteria lulus:
  - Retry berjalan sesuai aturan
  - Error message dapat ditindaklanjuti
  - Sistem tidak stuck

## 5) Rollback drill test

- Tujuan: memastikan rollback darurat bisa dilakukan cepat.
- Skenario:
  - Simulasi cutover gagal lalu rollback penuh.
  - Simulasi rollback parsial per modul.
- Kriteria lulus:
  - Rollback selesai < 5 menit
  - Health kembali normal

---

## Template hasil test (PASS/FAIL + evidence)

```md
### Hasil Uji: <nama test>
- Tanggal: YYYY-MM-DD
- Penguji: <nama>
- Scope fitur/modul: <fitur>
- Jalur diuji: Legacy / New / Dual
- Hasil: PASS / FAIL
- Evidence:
  - Log: <path atau link>
  - Screenshot/rekaman: <path atau link>
  - Output sample: <ringkas>
- Catatan:
  - TODO:
- Tindak lanjut jika FAIL:
  - TODO:
```

## Checklist eksekusi test plan

- [ ] Golden tests selesai untuk semua fitur prioritas.
- [ ] Contract tests lulus.
- [ ] Integration tests panel-orchestrator-worker lulus.
- [ ] Failure-mode tests tervalidasi.
- [ ] Rollback drill test lulus (< 5 menit).
