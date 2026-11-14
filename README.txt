JGTC STORE ENHANCED v4 - TAHAP 4

Isi arsip:
- index.html
- firestore.rules

Pengembangan Tahap 4:
1. Perlindungan data pesanan:
   - Dokumen orders tidak dapat dihapus melalui Firestore client SDK.
   - Pemilik hanya dapat mengubah field status; data pembeli, item, nominal, waktu, dan identitas pesanan dikunci setelah dibuat.
   - Transisi status hanya diizinkan dari "Menunggu Verifikasi" ke "LUNAS / TERVERIFIKASI" atau "DIBATALKAN".
   - Status final tidak dapat diubah kembali melalui aturan Firestore.
2. Audit perubahan status:
   - Setiap perubahan status ditulis ke koleksi orderStatusLogs.
   - Update pesanan dan pembuatan log dilakukan dalam satu Firestore write batch (atomik).
   - Log mencatat ID pesanan, status sebelumnya, status baru, waktu server, dan UID Owner.
   - Log hanya dapat dibaca Owner serta tidak dapat diedit/dihapus melalui client SDK.
   - Riwayat 20 perubahan terbaru untuk pesanan ditampilkan pada modal detail.
3. Pengamanan UI:
   - Tombol hapus pesanan dihilangkan dari daftar transaksi.
   - Fungsi deleteOrder menolak penghapusan sebagai perlindungan tambahan di UI.
   - Verifikasi status tetap meminta konfirmasi dan memeriksa sesi Owner.
4. Koleksi katalog, format dokumen orders, ID Owner, dan konfigurasi Firebase yang sudah ada dipertahankan.

PENTING SEBELUM DIPASANG:
- Terapkan firestore.rules v4 ke Firebase Console > Firestore Database > Rules.
- Jangan hanya mengganti index.html tanpa memperbarui Firestore Rules; fungsi audit dan penguncian transaksi memerlukan aturan baru.
- Lakukan backup Firestore terlebih dahulu.
- Dokumen orders lama yang statusnya bukan persis "Menunggu Verifikasi" tidak dapat dipindah statusnya menggunakan aturan transisi baru. Periksa data lama sebelum deployment.
- Data order yang sudah ada tidak otomatis memiliki audit log historis. Log mulai tercatat sejak versi ini dipasang.
- Tidak ada migrasi atau penghapusan dokumen lama.

PENGUJIAN YANG SUDAH DILAKUKAN DI LINGKUNGAN PEMBUATAN:
- Pemeriksaan sintaks JavaScript inline/module dengan node --check: lulus.
- Pemeriksaan isi dan integritas ZIP: lulus.
- Pemeriksaan bahwa tombol hapus pesanan tidak lagi ditampilkan: lulus.

PENGUJIAN YANG BELUM DILAKUKAN:
- Simulasi Firestore Rules melalui Firebase Emulator.
- Login Owner dan transaksi aktual pada project Firebase.
- Uji batch status + audit log, query riwayat, serta tampilan HP/desktop pada browser nyata.
- Deploy ke hosting produksi.

CATATAN KEAMANAN:
- Aturan Firestore adalah pengaman utama; validasi di browser saja tidak cukup.
- UID Owner pada Rules tetap sama dengan versi sebelumnya.
- Firebase API key pada aplikasi web bukan rahasia; keamanan data ditentukan oleh Authentication dan Firestore Rules.
- Karena penghapusan pesanan dinonaktifkan, arsip transaksi dipertahankan. Jika suatu saat dibutuhkan pengarsipan, sebaiknya dirancang sebagai status/fitur arsip terpisah dan diuji dengan aturan server-side.
