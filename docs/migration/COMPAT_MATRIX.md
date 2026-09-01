# COMPAT_MATRIX

Matriks ini memverifikasi kesetaraan perilaku antara jalur lama (legacy) dan jalur baru (orchestrator).

## Matriks kompatibilitas old vs new

| Fitur | Legacy path tested | New orchestrator path tested | Output parity (Match/Mismatch) | Performance delta | Error handling parity | Status (PASS/FAIL) | Catatan |
|---|---|---|---|---|---|---|---|
| Generate ringkasan laporan | `TODO: legacy/reporting/generate.py --from 2026-08-01 --to 2026-08-31` | `POST /actions/reporting/summary/run` | Match | `TODO: <= +10%` | Match | PASS | Output sama, format waktu divalidasi |
| Jalankan aksi modul terpadu | `TODO: bash legacy/actions/run_all.sh` | `POST /actions/{module}/{action}/run` | TODO: verifikasi | `TODO:` | TODO: verifikasi | FAIL | Perlu normalisasi kode error |
| Ambil status job | `TODO: python legacy/status/check_status.py JOB123` | `GET /actions/JOB123/status` | Match | `TODO: <= +5%` | Match | PASS | Field `error` kosong jika sukses |
| Integrasi panel ke backend | `TODO: node legacy/panel/api_client.js` | `frontend -> backend orchestrator` | TODO: verifikasi | `TODO:` | TODO: verifikasi | FAIL | Uji E2E panel belum selesai |
| Retry saat dependency down | `TODO: legacy/workers/retry_worker.py` | `worker/tasks.py` | TODO: verifikasi | `TODO:` | TODO: verifikasi | FAIL | Uji timeout/retry belum lengkap |

## Kriteria kelulusan migrasi per fitur

- [ ] Output parity = **Match** untuk minimal 3 dataset representatif (normal, edge, invalid input).
- [ ] Delta performa masih dalam batas yang disetujui tim (default: maksimal +10% latency).
- [ ] Error handling parity konsisten (kode status, pesan error, retry behavior).
- [ ] Bukti uji (log/screenshot/report) dilampirkan.
- [ ] Status fitur di matriks = **PASS**.

## Go/No-Go gate untuk cutover

### GO (boleh cutover)

- [ ] Semua fitur prioritas bisnis status **PASS**.
- [ ] Tidak ada **High risk** item yang masih `FAIL`.
- [ ] Rollback drill lulus dengan waktu pemulihan < 5 menit.
- [ ] Monitoring health/error rate aktif.

### NO-GO (tunda cutover)

- [ ] Ada mismatch output pada fitur kritikal.
- [ ] Error rate jalur baru lebih tinggi dari baseline yang disepakati.
- [ ] Rollback belum teruji.
- [ ] Owner teknis belum sign-off.
