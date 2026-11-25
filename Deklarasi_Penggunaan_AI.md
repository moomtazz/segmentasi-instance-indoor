# Deklarasi Penggunaan AI

## Alat yang dipakai

Claude Code dengan model Claude Opus 5.5 dari Anthropic dipakai untuk menulis kode notebook Kaggle, menyusun panduan dan analisis, mengatur repositori Git, serta menjalankan perintah Kaggle CLI dan Roboflow dari laptop. Pemotretan, pemilihan kondisi foto, keputusan desain eksperimen (model, variabel, platform), eksekusi dan pemantauan notebook di Kaggle, pemeriksaan hasil, serta presentasi dilakukan sendiri.

## Prompt utama

Prompt di bawah disalin apa adanya dari percakapan, diurutkan menurut waktu.

| Tanggal | Prompt | Tujuan |
|---|---|---|
| 17-11-2026 | "halo kerjakan utk ...\Instance Segmentation\10 - Segmentasi Instance dengan Deep Learning.pdf" disertai pesan asisten tentang link dataset pengganti di Roboflow | Membaca tugas dan menyusun rencana serta notebook |
| 17-11-2026 | Jawaban atas pertanyaan AI: "NRP full ku 5054241031", "Kaggle", "YOLO + Mask R-CNN" | Menentukan variabel NRP, platform, dan model |
| 17-11-2026 | "apa aja baranfnya> ditentukan?" | Menentukan kelas yang dianotasi pada foto sendiri |
| 23-11-2026 | "lihat @download.txt" (log Kaggle versi 1 yang gagal) | Mencari penyebab error dan memperbaikinya |
| 24-11-2026 | "oke udah ku run lagi, sekarang jelasin apa yg sebenernya sedang kita lakukan" | Memahami alur tugas |
| 24-11-2026 | "aku ga tulis, tp kan bisa dilihat kalo itu burem, gelap, dll" | Mengelompokkan foto ke kondisi normal dan sulit |
| 25-11-2026 | "ini ternyata ada reponya" disertai tabel komponen tugas | Menyiapkan hipotesis, repositori Git, dan notebook demo |
| 25-11-2026 | "udah selesai dan hasilsnya ada di folder ini ..." (output Kaggle versi 3) | Membaca hasil dan menyusun 5 studi kasus kegagalan |
| 25-11-2026 | "oke coba jelaskan apa yg kurang dan blm dikerjakan" lalu "1 2 3 kerjakan semua" | Melengkapi variabel NRP untuk ketiga model, deklarasi AI, dan uji demo |

## Bagian yang dikerjakan dengan bantuan AI

| Bagian | Peran AI | Pemeriksaan yang dilakukan sendiri |
|---|---|---|
| Notebook `segmentasi_instance_indoor.ipynb` | Menulis kode unduh data, pembersihan kelas typo, training, evaluasi, grafik, analisis kegagalan, oracle resolusi mask, dan uji varian | Menjalankan di Kaggle, membaca log, memeriksa angka dan gambar |
| Notebook `demo_inferensi.ipynb` | Menulis notebook demo inferensi satu foto | Mencoba menjalankannya sebelum sesi |
| Pengolahan foto sendiri | Membuang file duplikat, mengubah HEIC ke JPG dengan EXIF tetap ada, membuat tabel EXIF, dan mengusulkan kelompok kondisi tiap foto | Memotret, memeriksa dan menyetujui pengelompokan kondisi |
| Logbook dan hipotesis | Menyusun entri dan merumuskan hipotesis dari arsitektur model | Membaca, mengoreksi, dan memastikan tanggalnya sesuai kejadian |
| `Hasil_dan_Analisis.md` dan `PROGRES_WEEK11.md` | Menyusun tabel dari CSV hasil Kaggle dan menulis draf analisis 5 studi kasus | Mencocokkan setiap angka dengan CSV dan gambar |
| Repositori Git | Membuat `.gitignore`, README, dan commit bertahap, lalu mengunggah ke GitHub | Memilih nama dan visibilitas repositori |

## Kasus saran atau kode AI yang salah

| Saran atau kode AI | Bagaimana kesalahannya ketahuan | Perbaikan | Bukti |
|---|---|---|---|
| Fungsi `gt_of` di notebook membuang mask kosong dengan `masks.reshape(len(masks), -1).any(1)`. Kode ini mengasumsikan setiap citra punya minimal satu objek | Commit Kaggle versi 1 berhenti setelah epoch pertama Mask R-CNN dengan `ValueError: cannot reshape array of size 0 into shape (0,newaxis)`. Penyebabnya, ada citra validasi yang tidak punya objek sama sekali, sedangkan NumPy tidak bisa menebak ukuran `-1` untuk array berisi 0 elemen. Empat run YOLO tetap selesai karena evaluasinya memakai validator Ultralytics | Baris itu diganti dengan `masks.any(axis=(1, 2))`, yang tetap menghasilkan array kosong untuk citra tanpa objek, lalu notebook dijalankan ulang sebagai versi 2 | `logs/kaggle_v1_log.txt`, baris 4100–4140 |
| AI menyarankan memakai ulang bobot versi sebelumnya lewat Add Input → Your Work → output notebook yang sama, lalu langsung menulis di logbook bahwa "hasil YOLO dipakai ulang lewat Add Input" | Log versi 2 menulis "Output sebelumnya: tidak ada", sehingga keempat run YOLO ternyata dilatih ulang. Pada percobaan berikutnya, notebook yang sama memang tidak muncul di menu Your Work | Entri logbook yang keliru diperbaiki sesuai log. Bobot versi 2 diunggah sebagai dataset Kaggle privat `hasil-cpmk3`, dan notebook diberi kemampuan membaca `hasil_cpmk3.zip` dari dataset | `logs/kaggle_v2_log.txt` baris 18, entri logbook 24-11-2026 |
| Hipotesis H5 dari AI menduga bahwa mencerahkan foto redup dengan gamma 0,5 atau CLAHE akan menaikkan jumlah objek yang terdeteksi | Uji varian di versi 3 menunjukkan TP total tidak naik secara konsisten (CLAHE 16/15/16 dan gamma 12/15/13 vs asli 15/13/18), dan foto lab yang terang gagal dengan pola yang sama | Hipotesis tidak diubah. Penjelasannya ditulis di analisis: kegagalan di lab redup berasal dari kelas dan tampilan objek yang tidak ada di data latih, bukan dari gelapnya | `hasil_kaggle_v3/uji_varian_kegagalan.csv`, `Hasil_dan_Analisis.md` bagian 4 |
