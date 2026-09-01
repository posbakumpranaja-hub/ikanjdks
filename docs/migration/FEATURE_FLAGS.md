# FEATURE_FLAGS

Daftar feature flag untuk migrasi bertahap dari legacy ke struktur baru.

## Daftar flag

| Flag | Definisi | Default | Dampak saat ON | Strategi aktivasi |
|---|---|---|---|---|
| `FF_USE_NEW_ORCHESTRATOR` | Mengarahkan eksekusi utama ke orchestrator baru | `false` | Jalur baru dipakai sebagai default | Aktifkan bertahap: 10% → 50% → 100% |
| `FF_USE_LEGACY_PATH` | Memaksa fallback ke jalur lama | `true` | Menjaga kompatibilitas saat awal migrasi | Turunkan ke `false` setelah semua fitur PASS |
| `FF_ENABLE_PANEL_NEW_FLOW` | Panel memakai alur baru end-to-end | `false` | UI memanggil API orchestrator baru | Aktifkan setelah uji integrasi panel lulus |
| `FF_ENABLE_MODULE_ROLLOUT_<NAMA_MODUL>` | Rollout per modul | `false` | Modul tertentu pindah ke jalur baru | Aktifkan per modul berdasarkan risiko |
| `FF_STRICT_OUTPUT_PARITY_CHECK` | Validasi parity output ketat saat dual-run | `true` | Mismatch langsung ditandai | Tetap ON sampai deprecation legacy |

> `TODO:` finalisasi nama flag agar konsisten dengan implementasi aktual repository.

## Strategi aktivasi bertahap

1. **Pra-cutover**: `FF_USE_LEGACY_PATH=true`, `FF_USE_NEW_ORCHESTRATOR=false`.
2. **Canary**: aktifkan `FF_USE_NEW_ORCHESTRATOR=true` untuk subset trafik/modul.
3. **Scale-up**: naikkan trafik bertahap sambil pantau error/latency.
4. **Stabilisasi**: jika matriks kompatibilitas PASS, matikan fallback modul demi modul.
5. **Rollback cepat**: bila terjadi insiden, set kembali legacy flags dalam < 5 menit.

## Contoh blok `.env` migrasi

```env
# Migration flags
FF_USE_NEW_ORCHESTRATOR=false
FF_USE_LEGACY_PATH=true
FF_ENABLE_PANEL_NEW_FLOW=false
FF_ENABLE_MODULE_ROLLOUT_REPORTING=false
FF_ENABLE_MODULE_ROLLOUT_WORKER_RETRY=false
FF_STRICT_OUTPUT_PARITY_CHECK=true
```
