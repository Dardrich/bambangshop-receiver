# BambangShop Receiver App
Tutorial and Example for Advanced Programming 2025 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a Rocket web framework skeleton that you can work with.

As this is an Observer Design Pattern tutorial repository, you need to implement a feature: `Notification`.
This feature will receive notifications of creation, promotion, and deletion of a product, when this receiver instance is subscribed to a certain product type.
The notification will be sent using HTTP POST request, so you need to make the receiver endpoint in this project.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Receiver" folder.

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    ROCKET_PORT=8001
    APP_INSTANCE_ROOT_URL=http://localhost:${ROCKET_PORT}
    APP_PUBLISHER_ROOT_URL=http://localhost:8000
    APP_INSTANCE_NAME=Safira Sudrajat
    ```
    Here are the details of each environment variable:
    | variable                | type   | description                                                     |
    |-------------------------|--------|-----------------------------------------------------------------|
    | ROCKET_PORT             | string | Port number that will be listened by this receiver instance.    |
    | APP_INSTANCE_ROOT_URL   | string | URL address where this receiver instance can be accessed.       |
    | APP_PUUBLISHER_ROOT_URL | string | URL address where the publisher instance can be accessed.       |
    | APP_INSTANCE_NAME       | string | Name of this receiver instance, will be shown on notifications. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)
3.  To simulate multiple instances of BambangShop Receiver (as the tutorial mandates you to do so),
    you can open new terminal, then edit `ROCKET_PORT` in `.env` file, then execute another `cargo run`.

    For example, if you want to run 3 (three) instances of BambangShop Receiver at port `8001`, `8002`, and `8003`, you can do these steps:
    -   Edit `ROCKET_PORT` in `.env` to `8001`, then execute `cargo run`.
    -   Open new terminal, edit `ROCKET_PORT` in `.env` to `8002`, then execute `cargo run`.
    -   Open another new terminal, edit `ROCKET_PORT` in `.env` to `8003`, then execute `cargo run`.

## Mandatory Checklists (Subscriber)
-   [✅] Clone https://gitlab.com/ichlaffterlalu/bambangshop-receiver to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [✅] Commit: `Create Notification model struct.`
    -   [✅] Commit: `Create SubscriberRequest model struct.`
    -   [✅] Commit: `Create Notification database and Notification repository struct skeleton.`
    -   [✅] Commit: `Implement add function in Notification repository.`
    -   [✅] Commit: `Implement list_all_as_string function in Notification repository.`
    -   [✅] Write answers of your learning module's "Reflection Subscriber-1" questions in this README.
-   **STAGE 3: Implement services and controllers**
    -   [✅] Commit: `Create Notification service struct skeleton.`
    -   [✅] Commit: `Implement subscribe function in Notification service.`
    -   [✅] Commit: `Implement subscribe function in Notification controller.`
    -   [✅] Commit: `Implement unsubscribe function in Notification service.`
    -   [✅] Commit: `Implement unsubscribe function in Notification controller.`
    -   [✅] Commit: `Implement receive_notification function in Notification service.`
    -   [✅] Commit: `Implement receive function in Notification controller.`
    -   [✅] Commit: `Implement list_messages function in Notification service.`
    -   [✅] Commit: `Implement list function in Notification controller.`
    -   [✅] Write answers of your learning module's "Reflection Subscriber-2" questions in this README.

## My Reflections
#### Reflection Subscriber-1
1. In this tutorial, we used `RwLock<>` to synchronise the use of `Vec` of `Notifications`. Explain why it is necessary for this case, and explain why we do not use `Mutex<>` instead?

    Penggunaan `RwLock<>` dan `Vec<Notification>` diperlukan karena fitur read/write lock-nya memungkinkan banyak thread untuk membaca data secara bersamaan selama tidak ada operasi tulis, sementara hanya ada satu thread yang diizinkan melakukan operasi tulis. Hal ini cocok untuk kasus di mana operasi pembacaan lebih dominan dan sering dilakukan oleh banyak thread secara paralel (di kasus ini, `list_all_as_string`). Jika menggunakan `Mutex<>`, akses data akan dibatasi hanya satu thread dalam satu waktu (baik untuk membaca maupun menulis) sehingga menimbulkan bottleneck pada skenario dengan banyak pembaca. Dengan demikian, `RwLock<>` lebih efisien karena mengoptimalkan konkurensi operasi baca tanpa mengorbankan keamanan data

2. In this tutorial, we used `lazy_static` external library to define `Vec` and `DashMap` as a “`static`” variable. Compared to Java where we can mutate the content of a `static` variable via a `static` function, why did not Rust allow us to do so?

    Rust melarang mutasi langsung pada `static` untuk mencegah race condition dan menjamin thread safety pada level kompilasi. Berbeda dengan Java yang mengizinkan hal tersebut tanpa proteksi eksplisit (sehingga rentan akan kesalahan konkurensi), Rust by default mengharuskan semua variabel `static` bersifat immutable. Untuk mengakomodasi kebutuhan mutasi, Rust menyediakan library seperti `lazy_static` yang menginisialisasi variabel secara lazy dan menjadikannya `Singleton`. Safe mutation dilakukan melalui wrapper seperti `RwLock<>` atau `Mutex<>` yang mengatur akses konkurensi secara eksplisit. Pendekatan ini memastikan bahwa perubahan data hanya terjadi melalui mekanisme sinkronisasi yang terkontrol dan menghilangkan risiko inkonsistensi data di env multi-thread.

#### Reflection Subscriber-2
1. Have you explored things outside of the steps in the tutorial, for example: `src/lib.rs`? If not, explain why you did not do so. If yes, explain things that you have learned from those other parts of code.

    Ya, yang saya peroleh dari kode di luar tutorial, khususnya `src/lib.rs` adalah file ini berperan sebagai modul inti yang menyimpan komponen penting seperti struktur error response, root URL, dan konfigurasi aplikasi. Penggunaan `dotenvy` dan `Figment` memungkinkan penggabungan konfigurasi default dengan environment variable secara dinamis. Sementara itu, `AppConfig` diimplementasikan sebagai `Singleton` untuk memastikan konsistensi data. Fungsi error handling seperti `compose_error_response` untuk membantu standardisasi respons error, dan struktur modul yang terorganisasi memisahkan logika bisnis dengan infrastruktur dasar aplikasi.

2. Since you have completed the tutorial by now and have tried to test your notification system by spawning multiple instances of Receiver, explain how Observer pattern eases you to plug in more subscribers. How about spawning more than one instance of Main app, will it still be easy enough to add to the system?

    Ya, Observer pattern mempermudah penambahan Subscriber baru karena menerapkan Open-Closed Principle, di mana Observer (Subscriber) dapat diperluas tanpa mengubah kode inti Publisher (Main App). Setiap instance Main App mengelola daftar subscriber-nya sendiri secara independen, sehingga penambahan instance aplikasi baru tetap feasible selama Subscriber terdaftar melalui API yang disediakan. Jika diperlukan notifikasi lintas instance, solusi seperti komunikasi antar-instance atau sistem pub/sub terpusat bisa diimplementasikan. Mekanisme HTTP API yang ada memungkinkan fleksibilitas dalam pendaftaran subscriber ke instance aplikasi tertentu, menjaga skalabilitas sistem.

3. Have you tried to make your own Tests, or enhance documentation on your Postman collection? If you have tried those features, tell us whether it is useful for your work (it can be your tutorial work or your Group Project).

    Ya, menurut saya, pengujian melalui Postman dan pembuatan dokumentasi koleksi sangat bermanfaat. Testing memungkinkan verifikasi respons aktual dari aplikasi terhadap berbagai skenario request, termasuk validasi error handling dan struktur data. Dokumentasi yang mencakup deskripsi endpoint, parameter, contoh request-respons, serta penjelasan error meningkatkan daya kolaborasi tim dan mempermudah onboarding pengguna baru. Fitur automated testing di Postman juga membantu menjaga konsistensi fungsionalitas saat iterasi pengembangan, baik untuk keperluan tutorial maupun proyek kelompok. Kombinasi antara unit test untuk komponen spesifik dan end-to-end test via Postman menciptakan lapisan validasi yang komprehensif.