# Segmentasi Instance pada Lingkungan Indoor Nyata

Repositori Evaluasi CPMK 3 mata kuliah Visi Komputer. Tiga model segmentasi instance dari dua keluarga arsitektur dilatih pada [Panoptic Indoor Segmentation v5](https://universe.roboflow.com/panopticindoor/panoptic-indoor-segmentation), lalu diuji pada test set asli dan pada 56 foto ruangan yang diambil sendiri dengan iPhone 16 ([Roboflow `tes-ruangan-sendiri`](https://universe.roboflow.com/vk-a0mta/tes-ruangan-sendiri)).

| Kode | Model | Keluarga |
|---|---|---|
| M1 | YOLOv8n-seg | one-stage, mask dari prototype |
| M2 | YOLO11n-seg | one-stage, mask dari prototype |
| M3 | Mask R-CNN ResNet-50 FPN | two-stage, mask 28×28 per RoI |

NRP berakhiran 1, sehingga variabel yang diselidiki adalah ukuran input 320, 640, dan 960.

## Isi repositori

| Path | Isi |
|---|---|
| `segmentasi_instance_indoor.ipynb` | Notebook Kaggle untuk unduh data, training, evaluasi, domain shift, analisis kegagalan, dan eksperimen pembuktian |
| `demo_inferensi.ipynb` | Demo inferensi satu foto baru dengan ambang skor dan ukuran input yang bisa diubah |
| `logs/` | Log asli commit Kaggle versi 1 (gagal di Mask R-CNN) dan versi 2 |
| `hasil_kaggle_v2/` | `results.csv` setiap run, tabel evaluasi, waktu GPU, dan grafik dari versi 2 (tanpa bobot) |
| `daftar_foto_sendiri.csv`, `exif_foto_asli.csv` | Daftar 56 foto test set sendiri beserta kondisi dan metadata EXIF |
| `Logbook_CPMK3.md` | Logbook kendala, keputusan, hipotesis, dan waktu GPU |
| `Deklarasi_Penggunaan_AI.md` | Deklarasi penggunaan AI, termasuk kasus kode AI yang salah |

## Menjalankan ulang

Notebook utama dijalankan di Kaggle dengan GPU T4 dan Internet menyala. API key Roboflow disimpan di Kaggle Secrets dengan label `ROBOFLOW_API_KEY`. Untuk melewati training, tambahkan output versi sebelumnya lewat Add Input agar folder `hasil_cpmk3` ditemukan.
