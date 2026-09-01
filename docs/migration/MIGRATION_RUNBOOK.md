# MIGRATION_RUNBOOK

Runbook operasional migrasi aman dari struktur lama ke struktur baru tanpa memutus fitur yang berjalan.

## Ringkasan estimasi waktu

| Fase | Nama fase | Estimasi |
|---|---|---|
| 0 | Baseline & inventaris | 0.5 hari |
| 1 | Scaffold struktur baru | 0.5 hari |
| 2 | Compatibility adapter | 1 hari |
| 3 | Observability | 0.5 hari |
| 4 | Migrasi per modul | 2-3 hari |
| 5 | Integrasi panel | 1 hari |
| 6 | Controlled cutover | 0.5 hari |
| 7 | Deprecation plan | 0.5 hari |

---

## Fase 0 — Baseline & inventaris

- **Objective**: punya baseline fitur, performa, dan risiko.
- **Prerequisite**:
  - Akses branch kerja migrasi.
  - Daftar owner per modul.
- **Commands/aksi**:
  - Inventaris fitur ke `docs/migration/FEATURE_MAP.md`.
  - Catat baseline kompatibilitas di `docs/migration/COMPAT_MATRIX.md`.
- **Validation checks**:
  - Semua fitur utama tercatat.
  - Risiko migrasi sudah ditandai Low/Med/High.
- **Exit criteria**:
  - FEATURE_MAP terisi minimal fitur kritikal.
  - COMPAT_MATRIX punya baseline awal.

## Fase 1 — Scaffold struktur baru

- **Objective**: menyiapkan struktur target tanpa mengganggu jalur lama.
- **Prerequisite**:
  - Hasil fase 0 selesai.
- **Commands/aksi**:
  - Buat struktur folder/entrypoint baru (`TODO: sesuaikan path aktual`).
  - Pastikan kode lama tetap utuh.
- **Validation checks**:
  - Struktur baru bisa di-load.
  - Tidak ada perubahan perilaku pada entrypoint lama.
- **Exit criteria**:
  - Scaffold siap dipakai adapter.

## Fase 2 — Compatibility adapter

- **Objective**: mempertahankan backward compatibility saat transisi.
- **Prerequisite**:
  - Struktur baru tersedia.
- **Commands/aksi**:
  - Implementasi adapter per fitur lama (`TODO: daftar adapter final`).
  - Normalisasi output (`status/result/error`).
- **Validation checks**:
  - Jalur lama dan baru menghasilkan kontrak output yang sama.
- **Exit criteria**:
  - Adapter fitur prioritas selesai dan lolos smoke test.

## Fase 3 — Observability

- **Objective**: memastikan visibilitas error/performa sebelum cutover.
- **Prerequisite**:
  - Adapter berjalan.
- **Commands/aksi**:
  - Aktifkan logging terstruktur, metric, dan health check.
  - Definisikan alarm error-rate/latency.
- **Validation checks**:
  - Log memuat correlation/job ID.
  - Alarm dapat terpicu saat simulasi error.
- **Exit criteria**:
  - Dashboard observability siap dipakai saat migrasi.

## Fase 4 — Migrasi per modul

- **Objective**: memindahkan modul bertahap berdasarkan risiko.
- **Prerequisite**:
  - Fase 2-3 selesai.
- **Commands/aksi**:
  - Urutan: Low risk → Medium → High.
  - Untuk tiap modul: implementasi path baru, uji parity, update COMPAT_MATRIX.
- **Validation checks**:
  - Output parity = Match.
  - Tidak ada regresi pada fitur lama.
- **Exit criteria**:
  - Semua modul target status PASS pada matriks.

## Fase 5 — Integrasi panel

- **Objective**: panel terpadu memakai orchestrator baru dengan fallback legacy.
- **Prerequisite**:
  - Modul inti sudah PASS.
- **Commands/aksi**:
  - Hubungkan panel ke endpoint orchestrator.
  - Sediakan toggle fallback legacy (`TODO: nama flag final`).
- **Validation checks**:
  - Run action, cek status, cek log dari panel berhasil.
  - Fallback legacy dapat dipicu.
- **Exit criteria**:
  - Uji E2E panel lulus.

## Fase 6 — Controlled cutover

- **Objective**: alihkan trafik bertahap dan aman.
- **Prerequisite**:
  - Gate GO pada `COMPAT_MATRIX.md` terpenuhi.
- **Commands/aksi**:
  - Aktifkan feature flag bertahap (10% → 50% → 100%).
  - Pantau error-rate, latency, success-rate real-time.
- **Validation checks**:
  - KPI stabil di setiap tahap trafik.
  - Tidak ada incident sev tinggi.
- **Exit criteria**:
  - 100% trafik di path baru dalam kondisi stabil.

## Fase 7 — Deprecation plan

- **Objective**: menonaktifkan path lama secara terkendali.
- **Prerequisite**:
  - Cutover stabil sesuai periode observasi (`TODO: mis. 14 hari`).
- **Commands/aksi**:
  - Umumkan deprecation window.
  - Bekukan perubahan pada jalur lama.
  - Hapus path lama bertahap setelah persetujuan.
- **Validation checks**:
  - Tidak ada konsumen aktif pada endpoint/entrypoint lama.
- **Exit criteria**:
  - Legacy path dipensiunkan aman, dokumen diperbarui.

---

## Risiko utama dan mitigasi

| Risiko | Dampak | Mitigasi praktis |
|---|---|---|
| Mismatch output old vs new | Regresi fitur | Wajib golden test + contract test sebelum naik tahap |
| Cutover terlalu cepat | Downtime/incident | Gunakan rollout bertahap + gate GO/NO-GO |
| Observability kurang | Sulit diagnosis | Logging terstruktur + alarm sebelum fase 4 |
| Rollback lambat | Durasi gangguan panjang | Drill rollback rutin, target < 5 menit |
| Ketergantungan eksternal tidak stabil | Job gagal berantai | Retry policy + timeout + circuit breaker |
