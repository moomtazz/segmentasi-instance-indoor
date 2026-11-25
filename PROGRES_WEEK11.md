# Progres Week 11: Segmentasi Instance pada Lingkungan Indoor Nyata

Halaman ini merangkum bukti untuk komponen 1 (test set buatan sendiri), komponen 3 (hipotesis), dan hasil perbandingan model yang sudah dilatih. Rincian lengkap ada di [`Hasil_dan_Analisis.md`](Hasil_dan_Analisis.md) dan [`Logbook_CPMK3.md`](Logbook_CPMK3.md).

| Kode | Model | Keluarga |
|---|---|---|
| M1 | YOLOv8n-seg | one-stage, mask dari prototype |
| M2 | YOLO11n-seg | one-stage, mask dari prototype |
| M3 | Mask R-CNN ResNet-50 FPN | two-stage, mask 28×28 per RoI |

## Komponen 1 · Test set buatan sendiri

**56 foto** diambil dengan iPhone 16 pada 24-11-2026, terdiri dari 43 foto kamar kos (16.39–17.08) dan 13 foto laboratorium komputer (20.35–20.37). Dari 56 foto, **29 dalam kondisi sulit**, yaitu 12 redup, 4 oklusi, 4 sudut ekstrem, 3 burem, 2 backlight, dan 4 kombinasi. Foto dianotasi di project Roboflow [`tes-ruangan-sendiri`](https://universe.roboflow.com/vk-a0mta/tes-ruangan-sendiri) dengan nama kelas yang sama dengan dataset latih, menghasilkan 583 objek dari 46 kelas (versi 2, dipakai untuk angka di halaman ini). Pada 25-11-2026 anotasi dirapikan menjadi 555 objek (versi 3): 16 boneka yang berlabel `toy` diganti ke `teddy bear`, serta potongan kaki kursi dan kaki meja yang tercatat sebagai objek terpisah dihapus.

### Screenshot proses anotasi

| | |
|---|---|
| ![dataset](screenshot_anotasi/1_dataset_56_foto.jpg) | ![statistik](screenshot_anotasi/4_statistik_dataset.jpg) |
| Halaman dataset, 56 foto beserta label | Ringkasan dataset: 56 foto, 583 anotasi, 46 kelas |
| ![kamar](screenshot_anotasi/2_editor_normal_03_kamar.jpg) | ![lab](screenshot_anotasi/3_editor_normal_27_lab.jpg) |
| Editor anotasi foto kamar kos | Editor anotasi foto laboratorium |

Jumlah objek per kelas ada di [`screenshot_anotasi/5_jumlah_per_kelas.jpg`](screenshot_anotasi/5_jumlah_per_kelas.jpg).

### EXIF / metadata foto

Tabel lengkap untuk 56 foto ada di [`exif_foto_asli.csv`](exif_foto_asli.csv), dan pasangan nama file baru dengan file HEIC aslinya ada di [`daftar_foto_sendiri.csv`](daftar_foto_sendiri.csv).

| File | File asli | Waktu ambil | Kamera | ISO | Eksposur |
|---|---|---|---|---|---|
| `normal_01.jpg` | `IMG_1052.HEIC` | 2026:11:24 16:39:51 | Apple iPhone 16, 5,96 mm f/1.6 | 400 | 1/33 s |
| `sulit_04_redup.jpg` | `IMG_1064.HEIC` | 2026:11:24 16:47:25 | Apple iPhone 16 | 8000 | 1/17 s |
| `sulit_29_redup.jpg` | `IMG_1129.HEIC` | 2026:11:24 20:37:17 | Apple iPhone 16 | 8000 | 1/17 s |

Semua foto berukuran 5712×3213. Median ISO foto redup adalah 2850, sedangkan foto normal 500, sehingga metadata ini sekaligus menjelaskan noise tinggi pada foto redup.

### Performa test set asli vs test set sendiri (mask mAP50-95)

| Model | Test asli | Test asli, kelas foto sendiri | Foto sendiri | Sendiri normal | Sendiri sulit | Penurunan |
|---|---|---|---|---|---|---|
| M1 YOLOv8n-seg | 0,067 | 0,068 | 0,059 | 0,115 | 0,035 | −12% |
| M2 YOLO11n-seg | 0,079 | 0,081 | 0,052 | 0,089 | 0,061 | −35% |
| M3 Mask R-CNN | 0,084 | 0,093 | **0,071** | **0,126** | **0,062** | −24% |

![domain shift](hasil_kaggle_v3/gambar/domain_shift.png)

**Analisis domain shift.** Pembanding yang adil adalah test asli yang dibatasi pada kelas yang sama dengan foto sendiri, karena foto sendiri hanya memuat 46 dari 73 kelas. Secara keseluruhan ketiga model turun 12–35%, tetapi penurunannya tidak merata. Foto kos kondisi normal justru lebih tinggi daripada test asli karena objeknya besar dan jelas, sehingga seluruh penurunan datang dari foto sulit dan foto laboratorium. Recall di lab (0,08–0,09) lebih rendah daripada di kos (0,10–0,17) untuk ketiga model, karena monitor mati, PC tower, kursi beroda, dan meja panjang lab jarang ada di data latih yang didominasi ruangan rumah. Di antara kondisi sulit, redup dan backlight menurunkan recall paling banyak.

## Komponen 3 · Hipotesis

Hipotesis tertulis di bagian **Hipotesis** pada [`Logbook_CPMK3.md`](Logbook_CPMK3.md), bertanggal **25-11-2026**. Hipotesis ini **ditulis sesudah training** (versi 1 pada 23-11 dan versi 2 pada 24-11), tetapi **sebelum evaluasi test set sendiri** dijalankan (versi 3 pada 25-11). Tanggalnya tidak dimundurkan, dan isinya difokuskan pada eksperimen yang saat itu belum dijalankan.

| No | Hipotesis | Status setelah diuji |
|---|---|---|
| H1 | Mask R-CNN unggul di foto sendiri, terutama objek kecil, karena mask 28×28 dibuat per objek sedangkan prototype YOLO hanya 160×160 untuk seluruh foto | Didukung (AP kecil 0,028 vs 0,006 dan 0,000; oracle 0,99 vs 0,77) |
| H2 | YOLO menyamai Mask R-CNN untuk objek besar | Tidak didukung |
| H3 | Turun > 30%, terbesar di lab | Sebagian |
| H4 | Redup paling merusak | Didukung |
| H5 | Gamma/CLAHE memperbaiki foto redup | Tidak didukung |
| H6 | Salah kelas teddy bear/toy, cabinet/shelf, meja | Sebagian |

Penjelasan untuk hipotesis yang salah ada di bagian 4 [`Hasil_dan_Analisis.md`](Hasil_dan_Analisis.md).

## Hasil perbandingan model pada data latih

Ketiga model dilatih pada Panoptic Indoor Segmentation v5 (397 train, 166 valid, 359 test, 73 kelas setelah typo dibersihkan) di Kaggle GPU Tesla T4, dengan batas 1 jam GPU per model.

| Model | Valid (mask mAP50-95) | Test (mask mAP50-95) | Test mAP50 | Inferensi | Parameter | GPU latih |
|---|---|---|---|---|---|---|
| M1 YOLOv8n-seg | 0,169 | 0,067 | 0,111 | 97 ms | 3,4 juta | 0,39 jam |
| M2 YOLO11n-seg | **0,192** | 0,079 | 0,136 | 92 ms | 2,9 juta | 0,50 jam |
| M3 Mask R-CNN R50-FPN | 0,179 | **0,084** | **0,149** | 203 ms | 44,3 juta | 1,01 jam |

![kurva validasi](hasil_kaggle_v3/gambar/kurva_validasi.png)

YOLO naik cepat dan berhenti sendiri oleh early stopping (M1 setelah 23 menit, M2 setelah 30 menit), sedangkan Mask R-CNN naik lebih lambat dan memakai seluruh 60 menit. Nilai di split valid dihitung dengan evaluator yang sama dan cocok dengan angka validasi saat training, sehingga test yang lebih rendah berasal dari split test yang lebih sulit.

### Variabel NRP (digit akhir 1): ukuran input 320 / 640 / 960

![ukuran input](hasil_kaggle_v3/gambar/ukuran_input.png)

| Seri | 320 | 640 | 960 |
|---|---|---|---|
| YOLO11n-seg dilatih ulang, test asli | 0,055 | 0,079 | 0,082 |
| YOLO11n-seg dilatih ulang, foto sendiri | 0,026 | 0,052 | 0,072 |
| Mask R-CNN dilatih 640, test asli | 0,063 | 0,084 | 0,079 |
| Mask R-CNN dilatih 640, foto sendiri | 0,034 | 0,071 | 0,073 |

Input yang lebih besar paling membantu di foto sendiri, karena foto 5712×3213 berisi banyak objek kecil dan jauh yang hilang saat diperkecil ke 320.

## Bukti proses

- Log training asli: [`logs/`](logs) (versi 1 gagal di Mask R-CNN, versi 2 training, versi 3 evaluasi) dan `results.csv` setiap run di [`hasil_kaggle_v2/runs/`](hasil_kaggle_v2/runs)
- Waktu GPU: [`hasil_kaggle_v3/waktu_gpu.json`](hasil_kaggle_v3/waktu_gpu.json), total 3,30 jam training
- Logbook: [`Logbook_CPMK3.md`](Logbook_CPMK3.md)
- Deklarasi AI: [`Deklarasi_Penggunaan_AI.md`](Deklarasi_Penggunaan_AI.md)
