# FEATURE_MAP

Dokumen ini dipakai untuk memetakan fitur dari struktur lama ke struktur baru secara aman dan terukur.

## Panduan pengisian

1. Isi **1 baris per fitur** (jangan digabung).
2. Gunakan path absolut repo relatif (contoh: `legacy/scripts/run-report.sh`).
3. Isi **Input/Output** secara ringkas tapi dapat diuji.
4. Jika data belum tersedia, isi `TODO: ...`.
5. Ubah **Status migrasi** secara berkala: `Not Started` → `In Progress` → `Validated` → `Cutover`.

## Inventaris fitur lama → target baru

| Feature ID | Nama fitur | Lokasi kode lama (path) | Entrypoint lama (CLI/API/script) | Dependensi | Input | Output | Risiko migrasi | Target lokasi baru | Adapter yang digunakan | Status migrasi |
|---|---|---|---|---|---|---|---|---|---|---|
| F-001 | Generate ringkasan laporan | `TODO: legacy/reporting/generate.py` | `python legacy/reporting/generate.py` | pandas, sqlite | tanggal awal/akhir | file ringkasan `.json` | Low | `backend/app/modules/reporting/summary.py` | `ReportingLegacyAdapter` | Not Started |
| F-002 | Jalankan aksi modul terpadu | `TODO: legacy/actions/run_all.sh` | `bash legacy/actions/run_all.sh` | bash, curl | nama modul, payload | status eksekusi | Medium | `backend/app/orchestrator/service.py` | `ActionCompatAdapter` | In Progress |
| F-003 | Ambil status job | `TODO: legacy/status/check_status.py` | `python legacy/status/check_status.py <job_id>` | sqlite | job_id | status + error/result | Low | `backend/app/orchestrator/routes.py` | `StatusLegacyAdapter` | Not Started |
| F-004 | Integrasi panel lama ke backend | `TODO: legacy/panel/api_client.js` | `node legacy/panel/api_client.js` | node-fetch | module/action/params | respons API | Medium | `frontend/src/api.js` | `PanelApiCompatAdapter` | Not Started |
| F-005 | Retry saat dependency gagal | `TODO: legacy/workers/retry_worker.py` | scheduler/worker lama | redis/celery | job payload | retry log + final state | High | `worker/tasks.py` | `RetryPolicyAdapter` | Not Started |

## Definisi status migrasi

- **Not Started**: belum dikerjakan.
- **In Progress**: implementasi/adaptasi sedang berjalan.
- **Validated**: lulus parity test old vs new.
- **Cutover**: default traffic sudah ke path baru.
