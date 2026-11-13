# InvoiceGuard – Robot Verifikasi Invoice dengan Persetujuan Manusia

**Tema:** Human in the Lead · **Peserta:** Monikaa Panjaitan (individu) · **Platform:** UiPath Studio 2026.10 (Windows, VB)

## Masalah
Staf keuangan mencocokkan setiap invoice vendor dengan Purchase Order (PO) secara manual: buka PDF, cari nomor PO di Excel, bandingkan vendor dan total. Cara ini lambat (±5 menit per invoice) dan rawan terlewat, misalnya invoice duplikat atau harga yang dinaikkan.

## Solusi
Robot membaca semua invoice PDF, mengambil datanya, lalu mencocokkannya dengan Master PO.
- Invoice yang cocok ditandai **Lolos** secara otomatis.
- Invoice yang bermasalah **tidak diputuskan oleh robot**. Robot menampilkan jendela persetujuan, lalu staf memilih **Setujui / Tolak / Tunda** dan menulis catatan.
- Semua hasil dan keputusan manusia dicatat ke laporan Excel. Invoice yang selesai diproses dipindah ke folder arsip.

Aturan validasi: invoice duplikat, No. PO tidak terdaftar, nama vendor berbeda, total berbeda dari PO, dan data yang tidak terbaca.

## Tools / Teknologi
- UiPath Studio (Process, Windows, VB.NET)
- Paket: UiPath.System.Activities 26.10.0, UiPath.UIAutomation.Activities 26.10.8, UiPath.Excel.Activities 3.6.1, UiPath.PDF.Activities 3.25.3
- Activity utama: Read Range / Write Range (Workbook), Read PDF Text, Invoke Code (ekstraksi Regex & validasi), Input Dialog (persetujuan manusia), Add Data Row, Message Box, Log Message, Try Catch

## Kebutuhan
- Windows dengan UiPath Studio/Robot (Community gratis)
- Tidak perlu Microsoft Excel terpasang (memakai activity Workbook)
- Tidak memakai password, API key, maupun koneksi internet saat dijalankan

## Struktur Folder
```
InvoiceGuard/
├─ InvoiceGuard_Robot/      ← project UiPath (Main.xaml, project.json)
└─ Data/
   ├─ Master_PO.xlsx        ← daftar PO resmi (contoh)
   └─ Invoice_Masuk/        ← invoice PDF yang akan diperiksa (8 contoh)
```

## Cara Menjalankan
1. Salin folder `Data` ke `C:\Users\<nama>\Documents\InvoiceGuard\Data`. Jika lokasinya berbeda, ubah nilai variabel `strDataFolder` di Main.xaml.
2. Buka `InvoiceGuard_Robot/project.json` di UiPath Studio, lalu tunggu paket selesai diunduh.
3. Klik **Run** (F5).
4. Untuk setiap invoice yang perlu review, pilih Setujui/Tolak/Tunda dan isi catatan.
5. Di akhir muncul ringkasan. Laporan tersimpan di `Data/Laporan_Verifikasi_<tanggal>.xlsx`.
6. Untuk mengulang demo, pindahkan PDF dari `Data/Sudah_Diproses` kembali ke `Data/Invoice_Masuk`.

## Contoh Input & Output
| File | Kondisi | Hasil robot |
|---|---|---|
| Invoice_01, 02, 04, 06 | Sesuai PO | Lolos (otomatis) |
| Invoice_03 | Harga satuan naik (Rp 42.000 vs Rp 38.000) | Perlu Review – total beda |
| Invoice_05 | PO-2026-999 tidak ada di Master PO | Perlu Review – PO tidak terdaftar |
| Invoice_07 | Nama vendor & qty berbeda | Perlu Review – vendor & total beda |
| Invoice_08 | Nomor sama dengan Invoice_01 | Perlu Review – duplikat |

Output: `Laporan_Verifikasi_*.xlsx` (sheet *Hasil_Verifikasi*) dengan kolom Waktu_Proses, File, No_Invoice, PO_Number, Vendor_Invoice, Total_Invoice, Total_PO, Selisih, Status_Robot, Alasan, Keputusan_Manusia, Catatan_Reviewer.

Semua data contoh **fiktif** dan tidak berisi data pribadi.
