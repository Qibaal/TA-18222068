# BAB V IMPLEMENTASI

## V.1 Implementasi Warehouse Management Controller

Sub-bab ini menjabarkan detail implementasi sistem _Warehouse Management Controller_ (WMC), mencakup implementasi modul/_service_ untuk prototipe _Smart Warehouse_.

### V.1.1 Implementasi Modul

Implementasi sistem WMC dilakukan ke dalam tiga _service_ utama, yaitu _Authentication Service_, _Goods Service_, dan _Fleet Service_. Setiap _service_ diuraikan menjadi empat bagian: kelas _Entity_, _Controller_, _Service_, dan _Repository_ sesuai Class Diagram pada Gambar 4.12, diikuti oleh tabel daftar _endpoint_ dan tabel deskripsi berkas fisik.

#### V.1.1.1 Authentication Service

_Authentication Service_ bertanggung jawab atas proses registrasi, _login_, penerbitan dan verifikasi JSON Web Token (JWT), serta pengelolaan data pengguna (_User_) dan sesi (_Session_).

### Tabel 5.1 Daftar _endpoint_ _Authentication Service_

| No  | Method | Endpoint         |
| :-: | :----- | :--------------- |
|  1  | GET    | `/`              |
|  2  | GET    | `/docs/spec`     |
|  3  | GET    | `/docs`          |
|  4  | POST   | `/auth/register` |
|  5  | POST   | `/auth/login`    |
|  6  | GET    | `/protected`     |
|  7  | GET    | `/users`         |
|  8  | GET    | `/users/:id`     |
|  9  | POST   | `/users`         |
| 10  | PUT    | `/users/:id`     |
| 11  | DELETE | `/users/:id`     |
| 12  | GET    | `/sessions`      |
| 13  | POST   | `/sessions`      |

### Tabel 5.2 Deskripsi Berkas _Authentication Service_

| Nomor   | Nama File                                | Deskripsi                                                                       |
| :------ | :--------------------------------------- | :------------------------------------------------------------------------------ |
| 1       | `src/controllers/auth.controller.ts`     | Controller registrasi, login, dan verifikasi token JWT (AuthController, usulan) |
| 2       | `src/services/auth.service.ts`           | Logika bisnis autentikasi dan penerbitan JWT (AuthService, usulan)              |
| 3       | `src/middlewares/jwt.middleware.ts`      | Middleware verifikasi token JWT pada setiap _request_ masuk (usulan)            |
| 4       | `src/utils/hash.util.ts`                 | Utilitas _hashing_ dan verifikasi kata sandi (usulan)                           |
| 5       | `src/controllers/user.controller.ts`     | Endpoint pengelolaan data user, dipindah dari `goods-service`                   |
| 6       | `src/services/user.service.ts`           | Logika bisnis pengelolaan data user, dipindah dari `goods-service`              |
| 7       | `src/repositories/user.repository.ts`    | Akses data user ke basis data (UserRepository, usulan)                          |
| 8       | `src/controllers/session.controller.ts`  | Endpoint pengelolaan sesi login, dipindah dari `goods-service`                  |
| 9       | `src/services/session.service.ts`        | Logika bisnis pengelolaan sesi login, dipindah dari `goods-service`             |
| 10      | `src/repositories/session.repository.ts` | Akses data sesi ke basis data (SessionRepository, usulan)                       |

#### V.1.1.2 Goods Service

_Goods Service_ menangani data barang, lokasi penyimpanan, pemesanan, serta pemetaan AprilTag ke barang.

### Tabel 5.3 Daftar _endpoint_ _Goods Service_

_Endpoint_ Robot, Task, dan Robot Log telah dikeluarkan dari daftar ini dan direalokasi ke _Fleet Service_ (Tabel 5.5).

| No  | Method | Endpoint                                   |
| :-: | :----- | :----------------------------------------- |
|  1  | GET    | `/docs`                                    |
|  2  | GET    | `/orders`                                  |
|  3  | GET    | `/orders/user/:userId`                     |
|  4  | POST   | `/orders`                                  |
|  5  | GET    | `/items`                                   |
|  6  | GET    | `/items/history`                           |
|  7  | GET    | `/items/sku/:sku`                          |
|  8  | POST   | `/items`                                   |
|  9  | PUT    | `/items/:id`                               |
| 10  | DELETE | `/items/:id`                               |
| 11  | GET    | `/items/apriltag-mappings/apriltag/:tagId` |
| 12  | POST   | `/items/apriltag-mappings`                 |
| 13  | PUT    | `/items/apriltag-mappings/apriltag/:tagId` |
| 14  | DELETE | `/items/apriltag-mappings/apriltag/:tagId` |
| 15  | GET    | `/item-locations`                          |
| 16  | GET    | `/item-locations/:id`                      |
| 17  | GET    | `/item-locations/item/:itemId`             |
| 18  | POST   | `/item-locations`                          |
| 19  | PUT    | `/item-locations/:id`                      |
| 20  | DELETE | `/item-locations/:id`                      |
| 21  | GET    | `/order-items`                             |
| 22  | GET    | `/order-items/:id`                         |
| 23  | GET    | `/order-items/order/:orderId`              |
| 24  | POST   | `/order-items`                             |
| 25  | PUT    | `/order-items/:id`                         |
| 26  | DELETE | `/order-items/:id`                         |

### Tabel 5.4 Deskripsi Berkas _Goods Service_

| Nomor    | Nama File                                        | Deskripsi                                                                       |
| :------- | :----------------------------------------------- | :------------------------------------------------------------------------------ |
| 1        | `src/controllers/item.controller.ts`             | Endpoint item barang dan _mapping_ AprilTag                                     |
| 2        | `src/controllers/order.controller.ts`            | Endpoint pemesanan barang                                                       |
| 3        | `src/controllers/itemLocation.controller.ts`     | Endpoint lokasi penyimpanan item (tambahan)                                     |
| 4        | `src/controllers/orderItem.controller.ts`        | Endpoint detail item pada pesanan (tambahan)                                    |
| 5        | `src/services/item.service.ts`                   | Logika bisnis pengelolaan item barang                                           |
| 6        | `src/services/order.service.ts`                  | Logika bisnis pemesanan barang                                                  |
| 7        | `src/services/orderItem.service.ts`              | Logika bisnis detail item pesanan (tambahan)                                    |
| 8        | `src/repositories/item.repository.ts`            | Akses data item ke basis data (ItemRepository, usulan)                          |
| 9        | `src/repositories/order.repository.ts`           | Akses data order ke basis data (OrderRepository, usulan)                        |
| 10       | `src/repositories/orderItem.repository.ts`       | Akses data order item ke basis data (OrderItemRepository, usulan)               |
| 11       | `src/repositories/aprilTagMapping.repository.ts` | Akses data _mapping_ AprilTag ke basis data (AprilTagMappingRepository, usulan) |
| 12       | `src/lib/prisma.ts`                              | Inisialisasi Prisma client                                                      |
| 13       | `prisma/schema.prisma`                           | Definisi model Item, Order, OrderItem, AprilTagMapping (perlu dicek)            |

#### V.1.1.3 Fleet Service

_Fleet Service_ menangani seluruh logika operasional armada robot, yakni pencatatan robot dan log operasionalnya, penjadwalan _task_, orkestrasi pergerakan, deteksi tabrakan, serta eksekusi _workflow_ _storing_/_picking_.

### Tabel 5.5 Daftar _endpoint_ _Fleet Service_

_Endpoint_ Robot, Task, dan Robot Log berasal dari realokasi `goods-service`; _endpoint_ lain berasal dari `blob-service` dan `fleet-controller`. Kolom _Asal_ menunjukkan basis kode sumber setiap _endpoint_.

| No  | Method | Endpoint                     | Asal                   |
| :-: | :----- | :--------------------------- | :--------------------- |
|  1  | GET    | `/robots`                    | goods-service (pindah) |
|  2  | POST   | `/robots`                    | goods-service (pindah) |
|  3  | GET    | `/tasks`                     | goods-service (pindah) |
|  4  | GET    | `/tasks/:id`                 | goods-service (pindah) |
|  5  | GET    | `/tasks/robot/:robotId`      | goods-service (pindah) |
|  6  | POST   | `/tasks`                     | goods-service (pindah) |
|  7  | PUT    | `/tasks/:id`                 | goods-service (pindah) |
|  8  | DELETE | `/tasks/:id`                 | goods-service (pindah) |
|  9  | GET    | `/robot-logs`                | goods-service (pindah) |
| 10  | GET    | `/robot-logs/:id`            | goods-service (pindah) |
| 11  | GET    | `/robot-logs/robot/:robotId` | goods-service (pindah) |
| 12  | POST   | `/robot-logs`                | goods-service (pindah) |
| 13  | PUT    | `/robot-logs/:id`            | goods-service (pindah) |
| 14  | DELETE | `/robot-logs/:id`            | goods-service (pindah) |
| 15  | GET    | `/health`                    | blob-service           |
| 16  | \*     | `/apriltag-mappings/*`       | blob-service           |
| 17  | \*     | `/robot-mappings/*`          | blob-service           |
| 18  | \*     | `/orders/*`                  | blob-service           |
| 19  | GET    | `/docs`                      | blob-service           |
| 20  | GET    | `/health`                    | fleet-controller       |
| 21  | GET    | `/map`                       | fleet-controller       |
| 22  | GET    | `/queue`                     | fleet-controller       |
| 23  | GET    | `/robots`                    | fleet-controller       |
| 24  | GET    | `/reservations`              | fleet-controller       |
| 25  | POST   | `/reset`                     | fleet-controller       |
| 26  | POST   | `/gotoidle`                  | fleet-controller       |
| 27  | POST   | `/demo/forward`              | fleet-controller       |
| 28  | POST   | `/demo/turn/right`           | fleet-controller       |
| 29  | POST   | `/demo/turn/left`            | fleet-controller       |
| 30  | POST   | `/demo/dock`                 | fleet-controller       |
| 31  | POST   | `/demo/undock`               | fleet-controller       |
| 32  | POST   | `/demo/navigate`             | fleet-controller       |
| 33  | POST   | `/demo/storing`              | fleet-controller       |
| 34  | POST   | `/demo/picking`              | fleet-controller       |

### Tabel 5.6 Deskripsi Berkas _Fleet Service_

| Nomor    | Nama File                                    | Deskripsi                                                                     |
| :------- | :------------------------------------------- | :---------------------------------------------------------------------------- |
| 1        | `src/controllers/robot.controller.ts`        | Endpoint pengelolaan robot, dipindah dari `goods-service`                     |
| 2        | `src/services/robot.service.ts`              | Logika bisnis pengelolaan robot, dipindah dari `goods-service`                |
| 3        | `src/services/task.service.ts`               | Logika bisnis penjadwalan task, dipindah dari `goods-service`                 |
| 4        | `src/controllers/robotLog.controller.ts`     | Endpoint log operasional robot, dipindah dari `goods-service` (tambahan)      |
| 5        | `src/services/robotLog.service.ts`           | Logika bisnis log operasional robot, dipindah dari `goods-service` (tambahan) |
| 6        | `src/repositories/robot.repository.ts`       | Akses data robot ke basis data (RobotRepository, usulan)                      |
| 7        | `src/repositories/task.repository.ts`        | Akses data task ke basis data (TaskRepository, usulan)                        |
| 8        | `src/controllers/digitalTwin.controller.ts`  | Endpoint digital twin robot (DigitalTwinController, usulan - belum ada)       |
| 9        | `src/services/digitalTwin.service.ts`        | Logika bisnis digital twin robot (DigitalTwinService, usulan - belum ada)     |
| 10       | `src/services/notification.service.ts`       | Logika bisnis notifikasi (NotificationService, usulan - belum ada)            |
| 11       | `blob-service/src/index.ts`                  | Entry point `blob-service`                                                    |
| 12       | `blob-service/src/routes/apriltagMapping.ts` | Route _mapping_ AprilTag (isi belum dilihat)                                  |
| 13       | `blob-service/src/routes/order.ts`           | Route order (isi belum dilihat)                                               |
| 14       | `blob-service/src/routes/robotTagMapping.ts` | Route _mapping_ robot-tag (isi belum dilihat)                                 |
| 15       | `fleet-controller/cmd/main.go`               | Entry point `fleet-controller` (asumsi)                                       |
| 16       | `fleet-controller/internal/api/*.go`         | Handler HTTP health/map/queue/robots/reservations/reset/gotoidle/demo         |

---

## V.2 Implementasi Sistem Lokalisasi

Sub-bab ini menjabarkan detail implementasi Subsistem Lokalisasi yang berjalan sebagai komponen dalam _Fleet Controller_, mencakup implementasi perangkat keras serta implementasi perangkat lunak.

### V.2.1 Implementasi Hardware

Perangkat keras utama Subsistem Lokalisasi adalah kamera IP _fisheye_ 1080p (H.264, RTSP _over_ TCP) yang dipasang setinggi 60 cm di atas area lantai gudang $2,4 \times 1,2$ m, dilengkapi pencahayaan LED tambahan agar kontras _marker_ AprilTag tetap optimal.

_Marker_ yang digunakan adalah AprilTag keluarga Tag36h11 berukuran $8 \times 8$ cm, ditempelkan pada robot dan barang di area gudang karena keseimbangan terbaiknya antara keterbacaan jarak jauh dan ketahanan terhadap oklusi parsial.

![Gambar 5.1 Perangkat Keras Subsistem Lokalisasi](../images/bab_v/localization_hardware.jpg)
_Gambar 5.1 Perangkat Keras Subsistem Lokalisasi_

### V.2.2 Implementasi Software

Implementasi perangkat lunak Subsistem Lokalisasi disusun dalam folder `localisasi`, terdiri atas skrip entry point, package `tracker` yang memuat seluruh logika _pipeline_ deteksi, serta berkas konfigurasi dan kalibrasi pendukung. Tabel 5.7 merangkum deskripsi singkat setiap berkas.

### Tabel 5.7 Deskripsi Berkas pada Folder `localisasi`

| Nomor  | Nama File                          | Deskripsi                                                                                                             |
| :----- | :--------------------------------- | :-------------------------------------------------------------------------------------------------------------------- |
| 1      | `local_rtsp.py`                    | Script standalone untuk uji koneksi RTSP kamera & deteksi AprilTag secara lokal (di luar package `tracker`).          |
| 2      | `main.py`                          | Entry point utama aplikasi - inisialisasi detector lalu menjalankan mode video atau stream.                           |
| 3      | `register_tags.py`                 | Script one-time untuk mendaftarkan AprilTag baru ke goods-service & blob-service via API.                             |
| 4      | `requirements.txt`                 | Daftar dependency Python yang dibutuhkan project.                                                                     |
| 5      | `tracker/__init__.py`              | Inisialisasi package `tracker`, mengekspos fungsi-fungsi dari seluruh modul.                                          |
| 6      | `tracker/capture.py`               | Penanganan capture video - threaded reader, koneksi RTSP, inisialisasi sumber kamera/detector.                        |
| 7      | `tracker/config.py`                | Konstanta konfigurasi sistem (URL RTSP, resolusi, FPS, brightness, grid, dll).                                        |
| 8      | `tracker/field.py`                 | Transformasi perspektif (homography) antara koordinat pixel dan koordinat grid lapangan.                              |
| 9      | `tracker/fisheye.py`               | Utilitas koreksi distorsi lensa fisheye.                                                                              |
| 10     | `tracker/grid.py`                  | Membangun, menggambar, dan mencatat matriks occupancy grid platform (robot/goods/docking).                            |
| 11     | `tracker/modes.py`                 | Mode eksekusi utama - playback video file dan live stream (RTSP/USB).                                                 |
| 12     | `tracker/mqtt.py`                  | Klien MQTT untuk konek ke broker dan publish state grid/entitas dalam format JSON.                                    |
| 13     | `tracker/overlay.py`               | Visualisasi overlay - quad lapangan, label tag, HUD, dan logging terminal.                                            |
| 14     | `tracker/processing.py`            | Pemrosesan frame, manajemen state, dan handler keypress terpusat.                                                     |
| 15     | `tracker/tags.py`                  | Utilitas AprilTag - klasifikasi jenis tag, warna, dan orientasi.                                                      |
| 16     | `tracker/transformer.py`           | Kelas `WarehouseCoordinateTransformer` untuk transformasi koordinat kamera ke koordinat gudang menggunakan kalibrasi. |
| 17     | `tuned_config.json`                | File hasil tuning konfigurasi (parameter kalibrasi yang sudah dioptimasi).                                            |
| 18     | `warehouse_calibration_context.md` | Catatan/context kalibrasi warehouse (parameter kamera, referensi koordinat, dll).                                     |

![Gambar 5.2 Antarmuka Visualisasi Subsistem Lokalisasi](../images/bab_v/localization_software.jpg)
_Gambar 5.2 Antarmuka Visualisasi Subsistem Lokalisasi_

Kalibrasi _homography_ dilakukan satu kali saat _setup_ dengan menempatkan empat _marker_ referensi di sudut-sudut area lantai gudang yang diketahui posisi _grid_-nya. Setelah kalibrasi selesai, parameter disimpan dan dimuat secara otomatis setiap kali Subsistem Lokalisasi dijalankan ulang.

**Implementasi Publikasi MQTT**

Pada tahap keenam _pipeline_, seluruh hasil deteksi _frame_ dikodekan sebagai _payload_ JSON yang merepresentasikan _state_ penuh dan diterbitkan setiap 500 ms ke _topic location_ pada RabbitMQ _broker_.

![Gambar 5.3 Hasil Publikasi Data Posisi ke MQTT Broker](../images/bab_v/localization_mqtt.jpg)
_Gambar 5.3 Hasil Publikasi Data Posisi ke MQTT Broker_
