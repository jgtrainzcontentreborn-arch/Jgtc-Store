JGTC STORE ENHANCED v5 - TAHAP 5A-5D

ISI PAKET
- index.html: website dengan perubahan minimal pada pembuatan audit log.
- firestore.rules: aturan keamanan v5.
- README.txt: catatan perubahan, hasil pemeriksaan, dan instruksi deployment.
- BACKUP_TAHAP4/: salinan asli ketiga file dari arsip Tahap 4. Jangan unggah folder backup ke hosting.

TAHAP 5A - KEAMANAN FIRESTORE
1. Transisi status pesanan tetap hanya dari "Menunggu Verifikasi" ke "LUNAS / TERVERIFIKASI" atau "DIBATALKAN".
2. Update dokumen orders kini mensyaratkan dokumen orderStatusLogs dengan ID yang sama dengan ID pesanan dalam batch atomik yang sama.
3. Dokumen log harus mencatat status asal, status tujuan, waktu server (request.time), dan UID Owner yang sama dengan pengguna terautentikasi.
4. Log hanya dapat dibuat satu kali, hanya dapat dibaca Owner, serta tidak dapat diubah atau dihapus.
5. Validasi email dan karakter nomor WhatsApp diperketat di Rules. Format nomor dengan +, spasi, tanda kurung, titik, atau strip tetap diperbolehkan agar sesuai input yang diformat di website.
6. UID Owner, Firebase project config, koleksi katalog, dan aturan akses katalog/settings dipertahankan.

TAHAP 5B - KOMPATIBILITAS FUNGSI LAMA
- Perubahan JavaScript hanya mengganti ID audit log dari auto-ID menjadi ID pesanan yang deterministik, sesuai aturan keamanan baru.
- Form pemesanan, katalog, perhitungan harga, login Owner, detail pesanan, ekspor CSV, navigasi, dan fungsi lain tidak sengaja dirombak.
- Riwayat log lama dengan auto-ID tetap dapat dibaca melalui query orderId.

TAHAP 5C - PEMERIKSAAN
Pemeriksaan lokal yang dilakukan:
- Struktur ZIP sumber berhasil dibaca.
- Sintaks JavaScript pada seluruh blok script diperiksa dengan Node.js.
- Pemeriksaan duplikasi ID HTML dilakukan.
- Pemeriksaan perubahan memastikan referensi project Firebase dan UID Owner tidak berubah.
- Pemeriksaan statis memastikan Rules mensyaratkan log berpasangan dan melarang update/delete log.

Belum dapat diklaim lulus / belum dilakukan:
- Kompilasi Rules oleh Firebase Emulator atau Firebase CLI.
- Pengujian Rules di Firestore Rules Playground/Emulator.
- Login ke Firebase project sebenarnya dan transaksi end-to-end.
- Deployment Hosting/Firestore ke project produksi.
- Uji browser nyata Android/desktop.

TAHAP 5D - BACKUP DAN DEPLOYMENT
1. Simpan ZIP ini dan BACKUP_TAHAP4 di tempat aman.
2. Sebelum deploy, buat backup/export Firestore dari project jgtc-store-62ca6.
3. Terapkan firestore.rules ke Firebase Console > Firestore Database > Rules, lalu Publish.
4. Ganti file index.html di hosting dengan index.html dari root paket ini. Jangan unggah file backup.
5. Uji: buat pesanan baru, login Owner, buka detail, verifikasi satu pesanan, lalu pastikan status berubah dan satu dokumen log tercipta di orderStatusLogs dengan ID sama dengan ID pesanan.
6. Uji juga pembatalan pada pesanan uji terpisah. Pastikan status final tidak dapat diubah lagi.
7. Jika Rules menolak pesanan baru, periksa format nomor WhatsApp, email, dan kesesuaian harga katalog.

PENTING
- Paket ini belum di-deploy ke Firebase atau hosting. Perubahan file lokal bukan berarti project produksi sudah berubah.
- Deploy Rules dan website harus dilakukan sebagai satu rilis terkoordinasi. Jika hanya index.html baru yang dipasang sebelum Rules v5, batch audit memakai ID baru dapat ditolak oleh Rules lama; jika Rules v5 dipasang lebih dulu, kode website lama masih menggunakan auto-ID dan update status akan ditolak.
- Log historis lama tidak dimigrasikan atau dihapus. Log lama dengan auto-ID tetap dapat ditampilkan; aturan baru hanya mengikat transisi baru.
- ID log deterministik berarti satu dokumen log untuk satu pesanan, sesuai desain bahwa status pesanan hanya boleh berubah sekali dari pending ke status final.
- Tidak ada penghapusan dokumen orders atau data katalog dalam perubahan ini.


TAMBAHAN v5.1 - DOWNLOAD QRIS UNTUK PEMBELI
- Pada formulir pemesanan, setelah pembeli menampilkan QRIS, tersedia tombol "Download Gambar QRIS".
- Tombol hanya tampil jika admin sudah mengunggah QRIS utama.
- Gambar diunduh sebagai file gambar; bila server penyimpanan menolak akses lintas-origin (CORS), gambar dibuka di tab baru sebagai fallback agar pembeli dapat menyimpannya secara manual.
- Tidak mengubah pengaturan QRIS di Panel Admin, struktur Firestore, aturan keamanan, proses verifikasi pembayaran, atau data transaksi.
- Pengujian yang dilakukan pada paket: pemeriksaan sintaks JavaScript dan struktur HTML statis. Uji unduhan aktual di browser dan URL Firebase Storage produksi tetap perlu dilakukan setelah deployment.
