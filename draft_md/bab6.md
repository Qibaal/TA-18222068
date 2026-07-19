# BAB VI EVALUASI

Bab ini memaparkan proses evaluasi terhadap sistem _Warehouse Management Controller_ (WMC) dan Subsistem Lokalisasi yang telah diimplementasikan. Evaluasi bertujuan untuk mengukur sejauh mana sistem yang dikembangkan memenuhi spesifikasi yang telah ditetapkan. Untuk WMC, cakupan evaluasi difokuskan pada ketahanan sistem di bawah beban melalui _stress test_, sedangkan untuk Subsistem Lokalisasi, cakupan evaluasi meliputi akurasi pemenuhan tugas (_Task Fulfilment Accuracy_), akurasi pemetaan posisi, serta deteksi orientasi.

## VI.1 Metode Evaluasi

### VI.1.1 Metode Evaluasi Sistem WMC

Evaluasi sistem WMC difokuskan pada komponen yang secara langsung dipengaruhi oleh WMC sebagai lapisan perangkat lunak pengendali gudang. Mengingat peran WMC dalam mengatur manajemen inventori, koordinasi tugas robot, serta antarmuka pengguna, kriteria evaluasi utama WMC adalah _Stress Test_ untuk mengukur ketahanan dan performa sistem di bawah beban.

#### VI.1.1.1 Pengujian Stress Test - Pengukuran Ketahanan Sistem di Bawah Beban

Pengujian _stress test_ dilakukan untuk mengukur ketahanan dan performa sistem WMC ketika menerima beban permintaan yang tinggi secara bersamaan pada setiap _endpoint_ utama. Sistem dinyatakan memenuhi kriteria apabila seluruh _endpoint_ yang diuji tidak mengalami kegagalan dengan _error rate_ 0% dan tetap responsif di bawah beban, yang diukur melalui waktu respons rata-rata, persentil ke-95 (p95) dan persentil ke-99 (p99).

Metode pengujian dilakukan dengan mengirimkan sejumlah _request_ secara simultan ke setiap _endpoint_ pada ketiga _service_ menggunakan _load testing tool_. Setiap _endpoint_ diuji secara terpisah dengan mencatat waktu respons rata-rata, p95, p99, _error rate_, serta _throughput_. Hasil pengujian kemudian dibandingkan terhadap kriteria keberhasilan, yaitu tidak ada kegagalan _request_ selama pengujian berlangsung.

### VI.1.2 Metode Evaluasi Sistem Lokalisasi

Evaluasi Subsistem Lokalisasi mencakup tiga kriteria yang saling melengkapi, yaitu akurasi pemenuhan tugas (_Task Fulfilment Accuracy_), akurasi pemetaan posisi, serta deteksi orientasi.

#### VI.1.2.1 Pengujian Task Fulfilment Accuracy - Verifikasi Konsistensi Data

Pengujian ini memverifikasi bahwa sistem mampu mengarahkan robot ke lokasi target yang benar dengan akurasi 100%, tanpa _misplacement_ maupun ketidaksesuaian data inventori. Kriteria ini mencerminkan ketepatan sistem terintegrasi, yang bergantung pada keandalan lokalisasi posisi serta pembaruan status dan posisi barang pasca tugas.

Metode pengujian dilakukan dengan membandingkan data yang tercatat dalam basis data terhadap kondisi fisik aktual setelah serangkaian tugas pengambilan dan penempatan barang dieksekusi. Pengujian dilaksanakan dengan menjalankan seluruh kombinasi 4 sel _idle_ dan 8 rak _storage_, menghasilkan 32 _order storing_ dan 32 _order picking_, atau total 64 _task_. Setiap _order_ yang dibuat dicatat beserta lokasi tujuan yang ditetapkan oleh sistem. Setelah robot menyelesaikan tugas, data lokasi barang yang diperbarui oleh sistem dibandingkan dengan lokasi fisik aktual di gudang. Tingkat akurasi dihitung menggunakan formula berikut.

```
Task Fulfilment Accuracy = (Jumlah Tugas Berhasil Tanpa Kesalahan / Jumlah Total Tugas) × 100%
```

Sistem dinyatakan lulus pengujian apabila seluruh tugas yang dieksekusi memberikan hasil yang sesuai antara perintah sistem dengan kondisi fisik, sehingga akurasi mencapai 100%.

#### VI.1.2.2 Pengujian Akurasi Posisi dan Orientasi

Verifikasi dilakukan dengan menempatkan _marker_ Tag36h11 AprilTag (8×8 cm) secara bersamaan pada seluruh 32 sel _grid_ lantai gudang berukuran 8×4, dengan posisi dan orientasi yang telah diketahui secara pasti sebagai _ground truth_, serta pengujian orientasi pada empat kelipatan sudut (0°, 90°, 180° dan 270°) sesuai resolusi deteksi _heading_ sistem. _Payload_ MQTT yang dipublikasikan subsistem dibandingkan terhadap _ground truth_ untuk dua properti, yaitu posisi sel _grid_ (kolom, baris) dan sudut _heading_, dengan target akurasi 100% pada kedua properti di seluruh sel dan sudut yang diuji.

Rangkuman seluruh kriteria evaluasi Subsistem Lokalisasi beserta parameter target dan metode pengujiannya disajikan pada Tabel 6.2.

### Tabel 6.2 Rangkuman kriteria dan metode evaluasi Subsistem Lokalisasi

| No  | Kriteria Evaluasi        | Parameter Target                                                               | Metode Pengujian                                                                   |
| :-: | :----------------------- | :----------------------------------------------------------------------------- | :--------------------------------------------------------------------------------- |
|  1  | Task Fulfilment Accuracy | Akurasi 100%, tanpa _misplacement_, serta konsistensi data fisik dan inventori | Perbandingan data basis data terhadap kondisi fisik aktual pasca eksekusi tugas    |
|  2  | Akurasi Posisi           | Akurasi 100% pemetaan sel _grid_ pada seluruh 32 sel _grid_ 8×4                | Perbandingan _payload_ MQTT terhadap _ground truth_ posisi pada seluruh sel _grid_ |
|  3  | Akurasi Orientasi        | Akurasi 100% deteksi _heading_ pada 0°, 90°, 180°, 270°                        | Perbandingan _payload_ MQTT terhadap _ground truth_ orientasi pada tiap sudut      |

Ketiga metode evaluasi tersebut dirancang agar dapat dilaksanakan secara bertahap dan sistematis. Pengujian akurasi posisi dan orientasi dapat dilakukan secara mandiri terhadap subsistem lokalisasi, sementara pengujian akurasi pemenuhan tugas memerlukan integrasi penuh antara sistem WMC dengan subsistem robot dan _Fleet Controller_ dalam skenario operasional terintegrasi.

## VI.2 Hasil Verifikasi

Sub-bab ini menyajikan hasil pelaksanaan setiap metode evaluasi yang telah direncanakan pada Subbab VI.1, dipisahkan antara hasil verifikasi Sistem WMC pada Subbab VI.2.1 dan hasil verifikasi Sistem Lokalisasi pada Subbab VI.2.2.

### VI.2.1 Hasil Verifikasi Sistem WMC

#### VI.2.1.1 Hasil Pengujian Stress Test

Pengujian _stress test_ dilaksanakan dengan mengirimkan _request_ secara simultan ke sebelas _endpoint_ utama pada _Authentication Service_ dan _Goods Service_. Setiap _endpoint_ diukur waktu respons rata-rata, p95, p99, _error rate_ dan _throughput_ selama pengujian berlangsung. Hasil pengujian dirangkum pada Tabel 6.3.

### Tabel 6.3 Hasil pengujian stress test _endpoint_ WMC

| Endpoint                                   |   Avg    |  p(95)   |  p(99)   | Error Rate | Throughput  | Status   |
| :----------------------------------------- | :------: | :------: | :------: | :--------: | :---------: | :------- |
| POST /auth/login                           | 6,12 ms  | 13,49 ms | 52,36 ms |   0,00%    | 17,6 req/s  | Pass All |
| GET /items (Goods Service)                 | 5,06 ms  | 10,37 ms | 120,6 ms |   0,00%    | 99,4 req/s  | Pass All |
| POST /items (Goods Service)                | 10,58 ms | 18,2 ms  | 46,24 ms |   0,00%    | 19,8 req/s  | Pass All |
| GET /orders (Blob Service)                 | 5,12 ms  | 11,13 ms | 21,96 ms |   0,00%    | 19,9 req/s  | Pass All |
| GET /apriltag-mappings/status              | 5,54 ms  | 17,06 ms | 26,28 ms |   0,00%    | 19,9 req/s  | Pass All |
| POST /apriltag-mappings                    |   9 ms   | 15,26 ms | 20,87 ms |   0,00%    | 19,8 req/s  | Pass All |
| GET /orders                                | 5,93 ms  | 11,74 ms | 28,57 ms |   0,00%    | 19,9 req/s  | Pass All |
| POST /orders                               | 8,71 ms  | 15,29 ms | 24,63 ms |   0,00%    | 19,6 req/s  | Pass All |
| GET /apriltag-mappings/item/:itemId        | 6,21 ms  | 13,44 ms | 22,87 ms |   0,00%    | 19,83 req/s | Pass All |
| POST /apriltag-mappings/scan               | 8,45 ms  | 14,92 ms | 21,33 ms |   0,00%    | 19,79 req/s | Pass All |
| PATCH /apriltag-mappings/:mappingId/status | 10,13 ms | 17,58 ms | 24,61 ms |   0,00%    | 19,71 req/s | Pass All |

Seluruh sebelas _endpoint_ yang diuji menunjukkan _error rate_ sebesar 0,00%, yang berarti tidak ada _request_ yang gagal diproses selama pengujian berlangsung, sehingga memenuhi kriteria keberhasilan yang ditetapkan. Waktu respons _endpoint_ secara konsisten berada di bawah 55 ms pada persentil ke-99.

### VI.2.2 Hasil Verifikasi Sistem Lokalisasi

#### VI.2.2.1 Hasil Verifikasi Posisi dan Orientasi

Pengujian posisi dilaksanakan dengan menempatkan AprilTag pada seluruh 32 sel _grid_ 8×4 secara bersamaan, dan seluruh sel berhasil terdeteksi dan dipetakan ke koordinat _grid_ yang sesuai dengan _ground truth_, mencapai akurasi 100%. Tabel 6.4 menampilkan enam titik uji sebagai contoh representatif dari keseluruhan pengujian tersebut. Pengujian orientasi dilaksanakan pada empat sudut _heading_ (0°, 90°, 180°, 270°) dan seluruhnya terdeteksi dengan benar, sebagaimana dicontohkan pada Tabel 6.5.

### Tabel 6.4 Hasil verifikasi posisi Subsistem Lokalisasi

| Tag ID | Sel Grid yang Diharapkan (c, r) | Status  |
| :----- | :------------------------------ | :------ |
| 562    | (0, 1)                          | ✓ Lulus |
| 584    | (0, 2)                          | ✓ Lulus |
| 577    | (1, 2)                          | ✓ Lulus |
| 585    | (3, 1)                          | ✓ Lulus |
| 586    | (5, 1)                          | ✓ Lulus |
| 578    | (2, 3)                          | ✓ Lulus |

### Tabel 6.5 Hasil verifikasi orientasi Subsistem Lokalisasi

| Tag ID | Heading yang Diharapkan | Status  |
| :----- | :---------------------- | :------ |
| 584    | 0°                      | ✓ Lulus |
| 562    | 90°                     | ✓ Lulus |
| 578    | 180°                    | ✓ Lulus |
| 577    | 270°                    | ✓ Lulus |

#### VI.2.2.2 Hasil Pengujian Task Fulfilment Accuracy

Pengujian akurasi pemenuhan tugas dilaksanakan dengan mengeksekusi seluruh kombinasi skenario perjalanan antara 4 sel _idle_ dan 8 rak _storage_. Setiap sel _idle_ diuji terhadap 8 kemungkinan rak _storage_ tujuan, menghasilkan 32 skenario tugas _storing_ (_idle_ → _storage_), yang masing-masing dipasangkan dengan skenario tugas _picking_ kembali ke sel _idle_ asal (_storage_ → _idle_), sehingga total keseluruhan mencapai 64 skenario tugas. Data lokasi yang tercatat dalam basis data kemudian dibandingkan dengan kondisi fisik aktual di gudang untuk setiap skenario. Setiap _task_ diperiksa untuk memastikan tidak terjadi kesalahan lokasi rak atau _misplacement_ dan tidak ada ketidaksesuaian antara kondisi fisik dengan data inventori. Hasil pengujian disajikan pada Tabel 6.6.

### Tabel 6.6 Hasil pengujian Task Fulfilment Accuracy

| Tipe Task                    | Jumlah Task Dieksekusi | Task Tanpa Kesalahan Lokasi | Akurasi (%) |
| :--------------------------- | :--------------------: | :-------------------------: | :---------: |
| Storing (penempatan barang)  |           32           |             32              |    100%     |
| Picking (pengambilan barang) |           32           |             32              |    100%     |
| **Total keseluruhan**        |         **64**         |           **64**            |  **100%**   |

Seluruh 64 skenario _task_ yang dieksekusi pada seluruh kombinasi 4 sel _idle_ dan 8 rak _storage_ menunjukkan kesesuaian penuh antara lokasi target yang ditetapkan oleh sistem dengan kondisi fisik aktual di gudang. Tidak terdapat satu pun kejadian _misplacement_ maupun ketidaksesuaian data inventori, sehingga tingkat _Task Fulfilment Accuracy_ mencapai 100%, memenuhi target spesifikasi yang ditetapkan.

## VI.3 Pembahasan Hasil Evaluasi

Berdasarkan hasil verifikasi pada Subbab VI.2, seluruh kriteria evaluasi untuk sistem WMC dan Subsistem Lokalisasi telah terpenuhi. Pengujian _stress test_ pada _endpoint_ menunjukkan _error rate_ 0,00% yang menyatakan sistem WMC tetap responsif di bawah beban tinggi. Pengujian _Task Fulfilment Accuracy_ menunjukkan akurasi 100% dari 64 _task_ tanpa satu pun kejadian _misplacement_, mengonfirmasi keandalan sistem dan lokalisasi posisi dalam mengarahkan robot ke lokasi target yang benar. Verifikasi pemetaan posisi dan deteksi orientasi juga mencapai akurasi 100% di seluruh 32 sel _grid_ dan seluruh sudut _heading_ yang diuji, menunjukkan bahwa _pipeline_ Subsistem Lokalisasi berfungsi sesuai yang diharapkan.

Secara keseluruhan, seluruh aspek yang diuji, yaitu ketahanan sistem WMC, akurasi pemenuhan tugas, serta akurasi lokalisasi posisi dan orientasi, memenuhi target minimum yang disyaratkan.
