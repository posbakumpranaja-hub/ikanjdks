# ROLLBACK_RUNBOOK

Target: rollback layanan migrasi ke jalur legacy dalam **< 5 menit**.

## Trigger rollback

Rollback dipicu jika salah satu terjadi:
- Error rate jalur baru melewati ambang (`TODO: contoh >5% selama 5 menit`).
- Fitur kritikal gagal end-to-end.
- Data output mismatch pada transaksi penting.
- Latency melonjak di atas ambang operasional.

## Otorisasi trigger rollback

- **Primary**: Incident Commander (on-call).
- **Secondary**: Tech Lead migrasi.
- **Approval darurat**: jika primary tidak respons dalam 2 menit, secondary boleh eksekusi.

## Langkah teknis rollback darurat (run target < 5 menit)

1. **Aktifkan rollback flag**
   - Set flag migrasi ke mode legacy:
     - `FF_USE_NEW_ORCHESTRATOR=false`
     - `FF_USE_LEGACY_PATH=true`
2. **Reload/restart service**
   - Jalankan restart service aplikasi (`TODO: command runtime aktual`).
3. **Freeze cutover**
   - Hentikan rollout trafik baru (jika bertahap).
4. **Verifikasi health**
   - Cek endpoint health dan smoke test fitur kritikal.
5. **Komunikasi status**
   - Umumkan “rollback completed” di channel insiden.

## Checklist verifikasi pasca rollback

- [ ] Health endpoint status normal.
- [ ] Fitur kritikal berjalan di path legacy.
- [ ] Error rate kembali ke baseline.
- [ ] Tidak ada antrean job stuck kritikal.
- [ ] Incident note awal sudah diposting.

## Decision tree: full rollback vs partial rollback

```text
Insiden terdeteksi?
  └─ Tidak → lanjut monitor
  └─ Ya
      ├─ Hanya 1-2 modul non-kritikal terdampak?
      │    └─ Ya → Partial rollback (flag per modul), monitor 15 menit
      │    └─ Tidak
      ├─ Fitur kritikal/error rate tinggi/risiko data?
      │    └─ Ya → Full rollback segera (<5 menit)
      │    └─ Tidak → Partial rollback + eskalasi tech lead
```

## Template incident note

```md
### Incident Note - Rollback Migrasi
- Waktu deteksi: TODO:
- Trigger: TODO:
- Dampak pengguna: TODO:
- Keputusan: Partial rollback / Full rollback
- Eksekutor: TODO:
- Waktu rollback selesai: TODO:
- Status setelah rollback: TODO:
- Tindak lanjut 24 jam: TODO:
```
