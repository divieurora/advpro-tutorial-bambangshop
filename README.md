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
-   ✅ Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   ✅ Commit: `Create Subscriber model struct.`
    -   ✅ Commit: `Create Notification model struct.`
    -   ✅ Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   ✅ Commit: `Implement add function in Subscriber repository.`
    -   ✅ Commit: `Implement list_all function in Subscriber repository.`
    -   ✅ Commit: `Implement delete function in Subscriber repository.`
    -   ✅ Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   ✅ Commit: `Create Notification service struct skeleton.`
    -   ✅ Commit: `Implement subscribe function in Notification service.`
    -   ✅ Commit: `Implement subscribe function in Notification controller.`
    -   ✅ Commit: `Implement unsubscribe function in Notification service.`
    -   ✅ Commit: `Implement unsubscribe function in Notification controller.`
    -   ✅ Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   ✅ Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   ✅ Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   ✅ Commit: `Implement publish function in Program service and Program controller.`
    -   ✅ Commit: `Edit Product service methods to call notify after create/delete.`
    -   ✅ Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1
1. In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber is defined as an interface. Explain based on your understanding of Observer design patterns, do we still need an interface (or `trait` in Rust) in this BambangShop case, or a single Model `struct` is enough?

    Dalam kasus bambangshop, masih hanya terdiri dari satu jenis `Subscriber` sehingga single Model `struct` saja sudah cukup. Namun, jika nanti ditambahkan banyak observer berbeda jenis diperlukan `trait` untuk memudahkan implementasi untuk masing masing `Subscriber`.

2. `id` in `Program` and `url` in `Subscriber` is intended to be unique. Explain based on your understanding, is using `Vec` (list) sufficient or using `DashMap` (map/dictionary) like we currently use is necessary for this case?

    Penggunaan `DashMap` mempermudah pemetaan tiap produk ke subscriber karena `DashMap` memiliki struktur key dan value. Jika menggunakan `Vec`, harus dipastikan bahwa setiap `id` dan `url` yang ditambahkan unik sehingga diperlukan langkah tambahan untuk memeriksa keunikannya dan menjadi tidak efisien. Untuk kasus bambangshop yang membutuhkan _identifier_ unik, `DashMap` akan lebih cocok digunakan.

3. When programming using Rust, we are enforced by rigorous compiler constraints to make a thread-safe program. In the case of the List of Subscribers (`SUBSCRIBERS`) static variable, we used the `DashMap` external library for **thread-safe** `HashMap`. Explain based on your understanding of design patterns, do we still need DashMap or we can implement Singleton pattern instead?

    Dalam _programming_ dengan Rust, penggunaan `DashMap` lebih cocok untuk _multithreading_. Bambangshop menggunakan _multithreading_ sehingga `DashMap` diperlukan karena `SUBSCRIBERS` akan diakses oleh banyak thread. Singleton saja tidak relevan karena Singleton bertujuan untuk memastikan hanya ada satu instansi objek selama program berjalan, sedangkan kebutuhan kita adalah untuk memastikan keamanan akses data dalam skenario multi-threading. Dengan DashMap, kita dapat memastikan bahwa daftar subscriber terhadap produk kita dikelola secara aman dan efisien dalam satu struktur data.

#### Reflection Publisher-2
1. In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”. Model in MVC covers both data storage and business logic. Explain based on your understanding of design principles, why we need to separate “Service” and “Repository” from a Model?

    Sesuai dengan salah satu SOLID Principle yaitu _Single Responsibility Principle_, Service dan Repository perlu dibedakan karena memiliki tanggung jawab yang berbeda. Service lebih bertanggung jawab pada _business logic_ aplikasi, sedangkan Repository lebih bertanggung jawab pada _handling_ penyimpanan data. Pemisahan Service dan Repository akan sangat membantu dalam _maintainability_ proyek ini.

2. What happens if we only use the Model? Explain your imagination on how the interactions between each model (`Program, Subscriber, Notification`) affect the code complexity for each model?

    Jika hanya menggunakan Model, cenderung akan terjadi _coupling_ pada kode sehingga kompleksitas kode meningkat karena Model harus menangani semua hal termasuk _business logic_ dan penyimpanan data. Hal ini akan menyebabkan kesulitan ketika ingin _maintain_ kode, serta sulit untuk melakukan _testing_ untuk setiap komponen yang terpisah.

3. Have you explored more about Postman? Tell us how this tool helps you to test your current work. Maybe you want to also list which features in Postman you are interested in or feel like it’s helpful to help your Group Project or any of your future software engineering projects.

    Menurut saya, Postman akan sangat membantu dalam _testing_ karena saya bisa dengan muudah menguji _response_ HTTP dengan mengirim _request_ HTTP ke API yang sedang dikembangkan. Postman juga menyediakan fitur-fitur yang _advanced_ yang bermanfaat untuk Proyek Kelompok seperti _Collaborative Collections_ untuk mengelompokkan beberapa _request_ HTTP dalam satu koleksi yang sama sehingga memfasilitasi koordinasi dalam pengembangan dan pengujian API proyek kelompok.

#### Reflection Publisher-3
1. Observer Pattern has two variations: **Push model** (publisher pushes data to subscribers) and **Pull model** (subscribers pull data from publisher). In this tutorial case, which variation of Observer Pattern that we use?

    Observer Pattern yang digunakan pada tutorial ini adalah **Push Model** karena Publisher mengirimkan _update_ ke Subscriber setiap ada perubahan tanpa menunggu _request_ dari Subscriber.

2. What are the advantages and disadvantages of using the other variation of Observer Pattern for this tutorial case? (example: if you answer Q1 with Push, then imagine if we used Pull)

    Jika menggunakan **Pull Model** pada kasus bambangshop, terdapat beberapa keuntungan dan kekurangan. Keuntungannya adalah Subscriber hanya akan menerima data ketika ada _request_ sehingga _update-update_ yang tidak perlu tidak akan diterima dan sistem akan menjadi lebih optimal. Namun, kekurangannya adalah Subscribers harus lebih aktif meminta _update_ sehingga kode akan jadi lebih kompleks serta dapat menyebabkan keterlambatan _update_.

3. Explain what will happen to the program if we decide to not use multi-threading in the notification process.

    Jika dalam hal ini _multithreading_ tidak digunakan untuk proses notifikasi, maka proses notifikasi perlu dilakukan secara _sequential_ dan berulang. Artinya, setiap notifikasi akan dikirim ke setiap Subscriber secara berurutan, satu per satu, tanpa adanya paralelisme. Sehingga hal ini akan menyebabkan kurangnya efisiensi jika `SUBSCRIBERS` berisi data yang sangat besar maupun jika terdapat _delay_ dalam pengiriman notifikasi.