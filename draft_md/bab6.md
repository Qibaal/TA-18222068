# BAB VI EVALUASI

Bab ini memaparkan proses evaluasi terhadap sistem _Warehouse Management Controller_ (WMC) dan Subsistem Lokalisasi yang telah diimplementasikan. Evaluasi bertujuan untuk mengukur sejauh mana sistem yang dikembangkan memenuhi spesifikasi yang telah ditetapkan pada dokumen B200. Untuk WMC, cakupan evaluasi meliputi akurasi pemenuhan tugas dan ketahanan sistem di bawah beban (_stress test_), sedangkan untuk Subsistem Lokalisasi, cakupan evaluasi meliputi akurasi pemetaan posisi dan deteksi orientasi. Bab ini disusun menjadi tiga sub-bab: Subbab VI.1 memaparkan metode evaluasi yang digunakan, Subbab VI.2 menyajikan hasil verifikasi dari setiap metode tersebut, dan Subbab VI.3 membahas hasil evaluasi secara keseluruhan sebagai dasar penilaian kelayakan sistem.

## VI.1 Metode Evaluasi

### VI.1.1 Metode Evaluasi Sistem WMC

Evaluasi sistem WMC dirancang mengacu pada rencana verifikasi yang tertuang dalam dokumen B200, dengan memfokuskan penilaian pada komponen-komponen yang secara langsung dipengaruhi oleh WMC sebagai lapisan perangkat lunak pengendali gudang. Mengingat peran WMC sebagai sistem _backend_ yang mengatur manajemen inventori, koordinasi tugas robot, serta antarmuka pengguna, maka terdapat dua kriteria evaluasi utama yang akan diukur, yaitu _Task Fulfilment Accuracy_ dan _Stress Test_. Setiap kriteria dievaluasi melalui metode pengujian yang berbeda namun saling melengkapi untuk memperoleh gambaran yang komprehensif mengenai kualitas sistem secara keseluruhan.

#### a. Pengujian Task Fulfilment Accuracy — Verifikasi Konsistensi Data

Pengujian akurasi pemenuhan tugas bertujuan untuk memverifikasi bahwa sistem WMC mampu menghasilkan dan mengelola perintah tugas dengan tingkat akurasi sebesar 100%, tanpa terjadi kesalahan lokasi rak (_misplacement_) dan tanpa ketidaksesuaian antara kondisi fisik dengan data inventori yang tersimpan. Dari sisi WMC, kriteria ini mencerminkan kualitas logika _Fleet Order Service_ dalam menentukan lokasi target, serta ketepatan _Goods Detail Service_ dalam memperbarui status dan posisi barang setelah setiap tugas diselesaikan.

Metode pengujian dilakukan dengan membandingkan data yang tercatat dalam basis data WMC terhadap kondisi fisik aktual setelah serangkaian tugas pengambilan dan penempatan barang dieksekusi. Pengujian dilaksanakan dengan menjalankan 8 _order storing_ dan 8 _order picking_ (total 16 _task_) menuju 8 rak _storage_ yang berbeda, dengan robot memulai pergerakan dari 4 sel _idle_ yang berbeda secara bergantian. Setiap _order_ yang dibuat melalui antarmuka WMC dicatat beserta lokasi tujuan yang ditetapkan oleh sistem. Setelah robot menyelesaikan tugas, data lokasi barang yang diperbarui oleh sistem dibandingkan dengan lokasi fisik aktual di gudang. Tingkat akurasi dihitung menggunakan formula berikut:

```
Task Fulfilment Accuracy = (Jumlah Tugas Berhasil Tanpa Kesalahan / Jumlah Total Tugas) × 100%
```

Sistem dinyatakan lulus pengujian apabila seluruh tugas yang dieksekusi memberikan hasil yang sesuai antara perintah sistem dengan kondisi fisik, sehingga akurasi mencapai 100%.

#### b. Pengujian Stress Test — Pengukuran Ketahanan Sistem di Bawah Beban

Pengujian _stress test_ dilakukan untuk mengukur ketahanan dan performa sistem WMC ketika menerima beban permintaan yang tinggi secara bersamaan pada setiap _endpoint_ utama. Sistem dinyatakan memenuhi kriteria apabila seluruh _endpoint_ yang diuji tidak mengalami kegagalan (_error rate_ 0%) dan tetap responsif di bawah beban, yang diukur melalui waktu respons rata-rata, persentil ke-95 (p95), dan persentil ke-99 (p99).

Metode pengujian dilakukan dengan mengirimkan sejumlah _request_ secara simultan ke setiap _endpoint_ pada _Authentication Service_ dan _Goods Service_ menggunakan alat pengujian beban (_load testing tool_). Setiap _endpoint_ diuji secara terpisah dengan mencatat waktu respons rata-rata, p95, p99, _error rate_, serta _throughput_ (jumlah _request_ yang berhasil diproses per detik). Hasil pengujian kemudian dibandingkan terhadap kriteria keberhasilan, yaitu tidak ada kegagalan _request_ selama pengujian berlangsung.

Rangkuman seluruh kriteria evaluasi beserta parameter target dan metode pengujiannya disajikan pada Tabel 6.1.

### Tabel 6.1 Rangkuman kriteria dan metode evaluasi WMC

| No  | Kriteria Evaluasi        | Parameter Target (B200)                                                                       | Metode Pengujian                                                                       |
| :-: | :------------------------ | :----------------------------------------------------------------------------------------------- | :--------------------------------------------------------------------------------------- |
|  1  | Task Fulfilment Accuracy   | Akurasi 100%; tanpa _misplacement_; konsistensi data fisik dan inventori                          | Perbandingan data basis data WMC terhadap kondisi fisik aktual pasca eksekusi tugas        |
|  2  | Stress Test                | _Error rate_ 0% pada seluruh _endpoint_ di bawah beban                                            | Pengujian beban simultan pada _endpoint_ utama dengan alat pengujian beban                |

Kedua metode evaluasi tersebut dirancang agar dapat dilaksanakan secara bertahap dan sistematis. Pengujian _stress test_ dapat dilakukan secara mandiri dalam lingkungan pengembangan, sementara pengujian akurasi pemenuhan tugas memerlukan integrasi penuh antara sistem WMC dengan subsistem robot dan _Fleet Controller_ dalam skenario operasional terintegrasi.

### VI.1.2 Metode Evaluasi Sistem Lokalisasi

Verifikasi Subsistem Lokalisasi dilakukan dengan menempatkan _marker_ Tag36h11 AprilTag (8×8 cm) pada seluruh sel _grid_ lantai gudang berukuran 8×4 (32 sel), dengan posisi dan orientasi yang telah diketahui secara pasti sebagai _ground truth_. Pengujian orientasi dilakukan pada empat kelipatan sudut, yaitu 0°, 90°, 180°, dan 270°, sesuai dengan resolusi deteksi _heading_ yang digunakan oleh sistem.

Pengujian dilaksanakan dengan menempatkan banyak _marker_ AprilTag secara bersamaan pada seluruh 32 sel _grid_ untuk memastikan bahwa pembacaan posisi berhasil dilakukan secara konsisten pada setiap sel, tidak hanya pada sebagian titik sampel. _Payload_ MQTT yang dipublikasikan oleh subsistem kemudian dibandingkan terhadap _ground truth_ untuk dua properti: posisi sel _grid_ (kolom, baris) dan sudut orientasi (_heading_). Target keberhasilan adalah akurasi 100% pada kedua properti tersebut di seluruh sel _grid_ dan seluruh sudut orientasi yang diuji. Tabel pengujian yang disajikan pada Subbab VI.2.2 menampilkan sebagian titik uji sebagai representasi dari keseluruhan pengujian yang telah dilakukan pada seluruh 32 sel _grid_.

## VI.2 Hasil Verifikasi

Sub-bab ini menyajikan hasil pelaksanaan setiap metode evaluasi yang telah direncanakan pada Subbab VI.1, dipisahkan antara hasil verifikasi Sistem WMC pada Subbab VI.2.1 dan hasil verifikasi Sistem Lokalisasi pada Subbab VI.2.2.

### VI.2.1 Hasil Verifikasi Sistem WMC

#### VI.2.1.1 Hasil Pengujian Task Fulfilment Accuracy

Pengujian akurasi pemenuhan tugas dilaksanakan dengan mengeksekusi 8 tugas _storing_ dan 8 tugas _picking_ melalui sistem WMC menuju 8 rak _storage_ yang berbeda, dengan robot memulai pergerakan dari 4 sel _idle_ yang berbeda secara bergantian, kemudian membandingkan data yang tercatat dalam basis data dengan kondisi fisik aktual di gudang. Setiap _task_ diperiksa untuk memastikan tidak terjadi kesalahan lokasi rak (_misplacement_) dan tidak ada ketidaksesuaian antara kondisi fisik dengan data inventori. Hasil pengujian disajikan pada Tabel 6.2.

### Tabel 6.2 Hasil pengujian Task Fulfilment Accuracy

| Tipe Task                       | Jumlah Task Dieksekusi | Task Tanpa Kesalahan Lokasi | Akurasi (%) |
| :---------------------------------- | :-----------------------: | :----------------------------: | :-----------: |
| Storing (penempatan barang)         |             8               |                8                 |    100%      |
| Picking (pengambilan barang)        |             8               |                8                 |    100%      |
| **Total keseluruhan**               |          **16**            |             **16**              |  **100%**    |

Seluruh 16 _task_ yang dieksekusi menuju 8 rak _storage_ dengan titik awal dari 4 sel _idle_ yang berbeda menunjukkan kesesuaian penuh antara lokasi target yang ditetapkan oleh sistem WMC dengan kondisi fisik aktual di gudang. Tidak terdapat satu pun kejadian _misplacement_ maupun ketidaksesuaian data inventori, sehingga tingkat _Task Fulfilment Accuracy_ mencapai 100%, memenuhi target spesifikasi B200.

#### VI.2.1.2 Hasil Pengujian Stress Test

Pengujian _stress test_ dilaksanakan dengan mengirimkan _request_ secara simultan ke sebelas _endpoint_ utama pada _Authentication Service_ dan _Goods Service_. Setiap _endpoint_ diukur waktu respons rata-rata, p95, p99, _error rate_, dan _throughput_ selama pengujian berlangsung. Hasil pengujian dirangkum pada Tabel 6.3.

### Tabel 6.3 Hasil pengujian stress test _endpoint_ WMC

| Endpoint                                     | Avg     | p(95)    | p(99)    | Error Rate | Throughput  | Status   |
| :---------------------------------------------- | :-------: | :--------: | :--------: | :----------: | :------------: | :--------- |
| POST /auth/login                                 | 6,12 ms  | 13,49 ms  | 52,36 ms  |    0,00%     |  17,6 req/s     | Pass All   |
| GET /items (Goods Service)                       | 5,06 ms  | 10,37 ms  | 120,6 ms  |    0,00%     |  99,4 req/s     | Pass All   |
| POST /items (Goods Service)                      | 10,58 ms | 18,2 ms   | 46,24 ms  |    0,00%     |  19,8 req/s     | Pass All   |
| GET /orders (Blob Service)                       | 5,12 ms  | 11,13 ms  | 21,96 ms  |    0,00%     |  19,9 req/s     | Pass All   |
| GET /apriltag-mappings/status                    | 5,54 ms  | 17,06 ms  | 26,28 ms  |    0,00%     |  19,9 req/s     | Pass All   |
| POST /apriltag-mappings                          | 9 ms     | 15,26 ms  | 20,87 ms  |    0,00%     |  19,8 req/s     | Pass All   |
| GET /orders                                      | 5,93 ms  | 11,74 ms  | 28,57 ms  |    0,00%     |  19,9 req/s     | Pass All   |
| POST /orders                                     | 8,71 ms  | 15,29 ms  | 24,63 ms  |    0,00%     |  19,6 req/s     | Pass All   |
| GET /apriltag-mappings/item/:itemId              | 6,21 ms  | 13,44 ms  | 22,87 ms  |    0,00%     |  19,83 req/s    | Pass All   |
| POST /apriltag-mappings/scan                     | 8,45 ms  | 14,92 ms  | 21,33 ms  |    0,00%     |  19,79 req/s    | Pass All   |
| PATCH /apriltag-mappings/:mappingId/status       | 10,13 ms | 17,58 ms  | 24,61 ms  |    0,00%     |  19,71 req/s    | Pass All   |

Seluruh sebelas _endpoint_ yang diuji menunjukkan _error rate_ sebesar 0,00%, yang berarti tidak ada _request_ yang gagal diproses selama pengujian berlangsung, sehingga memenuhi kriteria keberhasilan yang ditetapkan. Waktu respons p99 tertinggi tercatat pada _endpoint_ `GET /items` sebesar 120,6 ms, sementara _endpoint_ lainnya secara konsisten berada di bawah 55 ms pada persentil ke-99.

### VI.2.2 Hasil Verifikasi Sistem Lokalisasi

### Tabel 6.4 Contoh hasil verifikasi posisi Subsistem Lokalisasi (representasi dari pengujian pada seluruh 32 sel grid 8×4)

| Tag ID | Sel Grid yang Diharapkan (c, r) | Status    |
| :------- | :--------------------------------- | :----------- |
| 562      | (0, 1)                              | ✓ Lulus      |
| 584      | (0, 2)                              | ✓ Lulus      |
| 577      | (1, 2)                              | ✓ Lulus      |
| 585      | (3, 1)                              | ✓ Lulus      |
| 586      | (5, 1)                              | ✓ Lulus      |
| 578      | (2, 3)                              | ✓ Lulus      |

### Tabel 6.5 Contoh hasil verifikasi orientasi Subsistem Lokalisasi (kelipatan 90°)

| Tag ID | Heading yang Diharapkan | Status  |
| :------- | :------------------------- | :--------- |
| 584      | 0°                          | ✓ Lulus    |
| 562      | 90°                         | ✓ Lulus    |
| 578      | 180°                        | ✓ Lulus    |
| 577      | 270°                        | ✓ Lulus    |

Pengujian posisi dilaksanakan dengan menempatkan AprilTag pada seluruh 32 sel _grid_ 8×4 secara bersamaan, dan seluruh sel berhasil terdeteksi dan dipetakan ke koordinat _grid_ yang sesuai dengan _ground truth_, mencapai akurasi 100%. Tabel 6.4 menampilkan enam titik uji sebagai contoh representatif dari keseluruhan pengujian tersebut. Pengujian orientasi dilaksanakan pada empat sudut _heading_ (0°, 90°, 180°, 270°) dan seluruhnya terdeteksi dengan benar, sebagaimana dicontohkan pada Tabel 6.5.

## VI.3 Pembahasan Hasil Evaluasi

Berdasarkan hasil verifikasi pada Subbab VI.2, seluruh kriteria evaluasi yang ditetapkan pada dokumen B200 untuk sistem WMC dan Subsistem Lokalisasi telah terpenuhi. Pada sisi WMC, pengujian _Task Fulfilment Accuracy_ menunjukkan akurasi 100% dari 16 _task_ (8 _storing_ dan 8 _picking_) yang dieksekusi menuju 8 rak _storage_ berbeda dengan titik awal dari 4 sel _idle_ yang berbeda, tanpa satu pun kejadian _misplacement_ atau ketidaksesuaian data inventori, yang mengonfirmasi bahwa logika _Fleet Order Service_ dalam menentukan lokasi target serta mekanisme pembaruan data pada _Goods Detail Service_ berjalan dengan benar dan andal. Pengujian _stress test_ pada sebelas _endpoint_ utama _Authentication Service_ dan _Goods Service_ menunjukkan _error rate_ 0,00% di seluruh _endpoint_, dengan waktu respons p99 tertinggi sebesar 120,6 ms pada _endpoint_ `GET /items`, sementara _endpoint_ lainnya secara konsisten berada di bawah 55 ms pada persentil ke-99. Hasil ini mengindikasikan bahwa sistem WMC tetap responsif dan tidak mengalami kegagalan pemrosesan permintaan sekalipun menerima beban tinggi secara bersamaan.

Pada sisi Subsistem Lokalisasi, verifikasi yang dilakukan pada seluruh 32 sel _grid_ lantai gudang berukuran 8×4 menunjukkan akurasi 100% untuk pemetaan posisi, dan verifikasi pada seluruh empat kelipatan sudut orientasi (0°, 90°, 180°, 270°) juga menunjukkan akurasi 100% untuk deteksi _heading_. Hasil ini mengonfirmasi bahwa _pipeline_ Subsistem Lokalisasi — mulai dari koreksi distorsi _fisheye_, transformasi _homography_, hingga _snap_ ke sel _grid_ diskrit — berfungsi dengan benar dan mampu melakukan pemetaan posisi serta deteksi orientasi secara andal di seluruh area lantai gudang.

Perlu dicatat bahwa data hasil _stress test_ yang disajikan pada Tabel 6.3 merupakan hasil pengujian awal dan masih bersifat sementara, sehingga berpotensi diperbarui seiring dengan pengujian lanjutan pada kondisi beban yang lebih tinggi atau skenario operasional yang lebih bervariasi. Demikian pula, tabel verifikasi posisi dan orientasi Subsistem Lokalisasi (Tabel 6.4 dan Tabel 6.5) hanya menampilkan sebagian titik uji sebagai representasi dari keseluruhan 32 sel _grid_ dan seluruh sudut orientasi yang telah diverifikasi secara penuh.

Secara keseluruhan, hasil evaluasi menunjukkan bahwa sistem _Warehouse Management Controller_ (WMC) dan Subsistem Lokalisasi yang dikembangkan telah memenuhi seluruh kriteria evaluasi yang ditetapkan. Ketiga aspek yang diuji, yaitu akurasi pemenuhan tugas, ketahanan sistem di bawah beban (_stress test_), serta akurasi lokalisasi posisi dan orientasi, semuanya menunjukkan hasil yang memenuhi atau melampaui nilai target minimum yang disyaratkan dalam spesifikasi B200. Hal ini mengonfirmasi bahwa WMC dan Subsistem Lokalisasi yang diimplementasikan layak digunakan sebagai komponen inti dalam sistem _Smart Warehouse_ Paragon Corp.
