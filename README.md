# BambangShop Publisher App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

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
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [X] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [X] Commit: `Create Subscriber model struct.`
    -   [X] Commit: `Create Notification model struct.`
    -   [X] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [X] Commit: `Implement add function in Subscriber repository.`
    -   [X] Commit: `Implement list_all function in Subscriber repository.`
    -   [X] Commit: `Implement delete function in Subscriber repository.`
    -   [X] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [X] Commit: `Create Notification service struct skeleton.`
    -   [X] Commit: `Implement subscribe function in Notification service.`
    -   [X] Commit: `Implement subscribe function in Notification controller.`
    -   [X] Commit: `Implement unsubscribe function in Notification service.`
    -   [X] Commit: `Implement unsubscribe function in Notification controller.`
    -   [X] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [ ] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [ ] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [ ] Commit: `Implement publish function in Program service and Program controller.`
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1

1. Menurut saya dalam kasus BambangShop ini, menggunakan struct untuk Subscriber sudah cukup karena semua subscriber memiliki perilaku yang sama dalam menerima notifikasi. Trait (interface) biasanya diperlukan ketika kita membutuhkan implementasi yang berbeda-beda untuk subscriber, misalnya jika ada subscriber email, webhook, atau in-app yang masing-masing memiliki cara penanganan notifikasi yang berbeda. Namun, jika di masa depan BambangShop membutuhkan tipe subscriber yang beragam, implementasi trait akan memberikan fleksibilitas lebih.

2. Menurut saya penggunaan DashMap dibandingkan Vec sangat tepat untuk kasus ini karena DashMap menawarkan pencarian dengan kompleksitas O(1) berdasarkan key (id product atau url subscriber), menjamin keunikan key secara alami, dan memberikan thread safety secara bawaan. Vec akan membutuhkan pencarian linear O(n) yang kurang efisien serta logika tambahan untuk memastikan keunikan dan penanganan konkurensi, sehingga DashMap lebih cocok untuk aplikasi web yang memerlukan akses cepat berdasarkan id/url.

3. Menurut saya implementasi saat ini sudah tepat menggunakan kombinasi Singleton pattern (melalui lazy_static!) dengan DashMap. Singleton pattern memastikan hanya ada satu instans SUBSCRIBERS yang digunakan di seluruh aplikasi, sementara DashMap memberikan kemampuan thread-safe agar daftar SUBSCRIBERS tersebut dapat diakses oleh banyak thread secara bersamaan tanpa menyebabkan race condition. Keduanya memang diperlukan: Singleton untuk memastikan konsistensi data dengan menggunakan satu sumber kebenaran, dan DashMap untuk memungkinkan konkurensi yang efisien dengan fine-grained locking. Jadi, kedua konsep ini tidak saling menggantikan melainkan saling melengkapi untuk menciptakan sistem yang thread-safe dan konsisten.


#### Reflection Publisher-2

1. Pemisahan "Service" dan "Repository" dari Model diperlukan karena menerapkan prinsip Single Responsibility Principle (SRP), di mana setiap kelas seharusnya hanya memiliki satu alasan untuk berubah. Repository menangani akses dan persistensi data, sementara Service menangani logika bisnis, sehingga kode menjadi lebih terstruktur, mudah diuji, dan dapat diubah tanpa memengaruhi komponen lain.

2. Jika hanya menggunakan Model, semua logika bisnis dan akses data akan terakumulasi dalam satu kelas, yang menyebabkan model menjadi terlalu besar dan kompleks. Interaksi antara Program, Subscriber, dan Notification akan meningkatkan ketergantungan antar model, membuat kode sulit dimaintain, dan perubahan pada satu model mungkin akan memengaruhi model lainnya.

3. Postman sangat membantu dalam pengujian API dengan memungkinkan pembuatan request HTTP (GET, POST, DELETE) tanpa perlu menulis kode front-end, menyimpan koleksi request untuk digunakan kembali, membuat environment variables untuk testing di berbagai lingkungan, serta fitur automasi testing dengan Newman yang sangat berguna untuk continuous integration dalam pengembangan proyek.


#### Reflection Publisher-3
