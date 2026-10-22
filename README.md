# Portal Pengaduan Kerusakan Fasilitas Kampus (React + Supabase)

Implementasi dari Laporan PjBL Fase 1 (Discovery & Security Requirements), Kelompok 4, UMRAH.

## Struktur
```
src/
  lib/          supabase client, konstanta, validator (foto & kata sandi)
  context/      AuthContext (session + profil/role dari database)
  components/   Layout, ProtectedRoute, ReportCard, Photo, StatusBadge
  pages/
    Login.jsx                    fitur 1: login + registrasi Pelapor
    pelapor/ReportForm.jsx       fitur 2: buat laporan + foto
    pelapor/MyReports.jsx        fitur 3: status & riwayat milik sendiri
    petugas/ReportQueue.jsx      fitur 4: ubah status + riwayat audit
    admin/Accounts.jsx           fitur 5: kelola akun
supabase/
  schema.sql                     tabel, RLS, audit log, storage bucket
  functions/admin-users/         Edge Function untuk buat/hapus akun
```

## Menjalankan
1. Buat proyek di supabase.com, jalankan `supabase/schema.sql` di SQL Editor.
2. `cp .env.example .env`, isi URL dan anon key.
3. `npm install && npm run dev`
4. Deploy fungsi admin: `supabase functions deploy admin-users`
5. Daftar satu akun lewat aplikasi, lalu jadikan Admin (query ada di akhir schema.sql).

## Pemetaan ke Security Requirements
| SR | Implementasi |
|----|--------------|
| SR-01 | RLS `reports_select`: Pelapor hanya baris miliknya; UUID acak; pesan error generik |
| SR-02 | Tanpa policy update; status berubah hanya lewat RPC `update_report_status` (khusus Petugas) |
| SR-03 | Tabel `report_logs` diisi oleh RPC, tanpa policy tulis (append-only) |
| SR-04 | Whitelist JPG/PNG, cek file signature, maks 2 MB (klien + bucket), nama file diacak |
| SR-05 | Kelola akun lewat Edge Function yang memverifikasi role Admin; service key hanya di server |
| SR-06 | Maks 10 laporan/jam per pengguna (RLS insert) + batas ukuran bucket |

Catatan: MFA untuk Petugas/Admin dan rate limiting login (SK-01) diatur di dashboard Supabase
(Authentication > Multi-Factor dan Rate Limits), bukan di kode aplikasi.
