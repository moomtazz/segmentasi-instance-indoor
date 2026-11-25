# Deklarasi Penggunaan AI

## Alat yang dipakai

Claude Code dengan model Claude Opus 5.5 dari Anthropic dipakai untuk membantu menulis kode notebook Kaggle, menyusun panduan pengerjaan, dan membantu menyusun laporan. Pemotretan, anotasi di Roboflow, eksekusi notebook di Kaggle, pemilihan foto kegagalan, dan pemeriksaan hasil dilakukan sendiri.

## Prompt utama

1. "jelaskan spesifikasi C:\Users\Mumtaza\Documents\myITS\SEM 5\Visi Komputer\Instance Segmentation\10 - Segmentasi Instance dengan Deep Learning.pdf" disertai pesan asisten tentang link dataset pengganti di Roboflow
2. Jawaban atas pertanyaan AI tentang pengaturan tugas, yaitu NRP 031, training di Kaggle, perbandingan YOLO dan Mask R-CNN

## Bagian yang dikerjakan dengan bantuan AI

| Bagian | Peran AI | Pemeriksaan yang dilakukan sendiri |
|---|---|---|
| Notebook `segmentasi_instance_indoor.ipynb` | Menulis kode unduh data, training, evaluasi, grafik, dan alat analisis kegagalan | Menjalankan di Kaggle, membaca log, memeriksa angka dan gambar |
| Panduan dan logbook | Menyusun logbook berdasarkan draft | Membuat draft logbook |
| Laporan | Menyusun laporan | Memberikan input yang digunakan untuk laporan |

## Kasus saran atau kode AI yang salah

| Saran atau kode AI | Bagaimana kesalahannya ketahuan | Perbaikan | Bukti |
|---|---|---|---|
| Fungsi `gt_of` di notebook membuang mask kosong dengan `masks.reshape(len(masks), -1).any(1)`. Kode ini mengasumsikan setiap citra punya minimal satu objek | Commit Kaggle versi 1 berhenti setelah epoch pertama Mask R-CNN dengan `ValueError: cannot reshape array of size 0 into shape (0,newaxis)`. Penyebabnya, ada citra validasi yang tidak punya objek sama sekali, sedangkan NumPy tidak bisa menebak ukuran `-1` untuk array berisi 0 elemen. Empat run YOLO tetap selesai karena evaluasinya memakai validator Ultralytics | Baris itu diganti dengan `masks.any(axis=(1, 2))`, yang tetap menghasilkan array kosong untuk citra tanpa objek, lalu notebook dijalankan ulang sebagai versi 2 | Log Kaggle versi 1 ([`logs/kaggle_v1_log.txt`](logs/kaggle_v1_log.txt), baris 30586) |
