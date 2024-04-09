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
-   [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [x] Commit: `Create Subscriber model struct.`
    -   [x] Commit: `Create Notification model struct.`
    -   [x] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [x] Commit: `Implement add function in Subscriber repository.`
    -   [x] Commit: `Implement list_all function in Subscriber repository.`
    -   [x] Commit: `Implement delete function in Subscriber repository.`
    -   [x] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [x] Commit: `Create Notification service struct skeleton.`
    -   [x] Commit: `Implement subscribe function in Notification service.`
    -   [x] Commit: `Implement subscribe function in Notification controller.`
    -   [x] Commit: `Implement unsubscribe function in Notification service.`
    -   [x] Commit: `Implement unsubscribe function in Notification controller.`
    -   [x] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
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
1. ***In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber is defined as an interface. Explain based on your understanding of Observer design patterns, do we still need an interface (or trait in Rust) in this BambangShop case, or a single Model struct is enough?***
Observer design pattern biasanya didefinisikan sebagai interface karena memperbolehkan beberapa concrete implementations, sehingga jika kita memiliki beberapa Observer dengan tipe berbeda-beda maka mereka dapat meng-*update* dengan cara yang berbeda-beda dari Publisher. Dalam Rust, biasanya ini direpresentasikan sebagai trait, yang dimana Observer dapat diimplementasikan dari banyak struct. Dalam kasus ini, jika seluruh Subscriber akan memiliki fields yang sama maka sebuah Model struct saja sudah cukup. Tapi sebenarnya tergantung juga apakah kita membutuhkan trait atau sebuah struct saja bergantung dari requirement aplikasinya.
   
2. ***Did in Program and url in Subscriber is intended to be unique. Explain based on your understanding, is using Vec (list) sufficient or using DashMap (map/dictionary) like we currently use is necessary for this case?***
Menggunakan `DashMap` lebih tepat daripada menggunakan `Vec` jika url pada Subscriber akan menjadi unique. Pada dasarnya untuk mencari sebuah Subscriber dalam `Vec` diperlukan running time O(n), sedangkan dengan DashMap kita hanya perlu O(1) time untuk mencari dan mendapatkan suatu Subscriber. Lalu juga, `DashMap` di desain untuk concurrent access yang dimana beberapa thread dapat membaca dan menulis data ke `DashMap` tanpa menyebabkan *data race*
   
3. ***When programming using Rust, we are enforced by rigorous compiler constraints to make a thread-safe program. In the case of the List of Subscribers (SUBSCRIBERS) static variable, we used the DashMap external library for thread-safe HashMap. Explain based on your understanding of design patterns, do we still need DashMap or we can implement Singleton pattern instead?***
Singleton pattern memastikan bahwa sebuah class hanya ada satu instance dan memberikan global point untuk akses, sehingga dipastikan bahwa hanya ada satu variable `SUBSCRIBERS` saja untuk keseluruhan aplikasi. Lalu `DashMap` sendiri adalah Hashmap yang merupakan *thread-safe* data structure yang memperbolehkan multiple thread untuk mengakses dan mengubah data `SUBSCRIBERS` tanpa menyebabkan *data race* atau isu concurrency lainnya. Jika kita hanya menggunakan Hashmap biasa dengan Singleton pattern, maka *thread-safety* itu akan hilang yang malah mengakibatkan data race jika aplikasinya adalah multi-threaded. Jadi kesimpulannya, karena aplikasi BambangShop adalah multi-threaded application dan `SUBSCRIBERS` akan diakses banyak thread maka `DashMap` lebbih cocok.

#### Reflection Publisher-2
1. ***In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”. Model in MVC covers both data storage and business logic. Explain based on your understanding of design principles, why we need to separate “Service” and “Repository” from a Model?***
Dalam MVC pattern, biasanya `Model` menjadi suatu bagian yang meng-*handle* terlalu banyak *responsibility*. Makanya dengan memisahkan `Service` dengan `Repository` dari `Model`, kita dapat menghindari masalah-masalah dan membuat code yang lebih *maintainable* dan *clean*. Dengan memisahkannya kita akan memenuhi Single Responsibility Principle (SRP), membuat sistem yang lebih "Decoupled" sehingga sistem lebih fleksibel, serta meningkatkan *Testability* dan *Reusability*.

2. ***What happens if we only use the Model? Explain your imagination on how the interactions between each model (Program, Subscriber, Notification) affect the code complexity for each model?***
Jika kita hanya menggunakan Model saja, maka setiap model (Program, Subscriber, Notification) akan menjadi lebih kompleks dan susah di-*maintain* karena harus meng-*handle* data access, business logic, dan interaksi dengan model lainnya secara sendiri-sendiri. Lalu model-model tersebut akan menjadi *tightly coupled* yang jika ada perubahan di satu model maka model lain juga akan terdampak, dimana ini lebih rawan menyebabkan bug. Terakhir, ini akan melanggar SRP karena tiap model meng-*handle* banyak *responsibility*.

3. ***Have you explored more about Postman? Tell us how this tool helps you to test your current work. Maybe you want to also list which features in Postman you are interested in or feel like it’s helpful to help your Group Project or any of your future software engineering projects.***
Setelah saya cari tahu lebih lanjut dengan mencoba-coba, Postman ini berguna untuk melakukan API Testing terhadap aplikasi yang telah dibuat apakah fungsionalitasnya sudah memenuhi kriteria yang kita mau dan berjalan semestinya. Kita dapat mengatur HTTP method untuk mengirimkan requestnya ke endpoint API yang dituju dan melihat apakah responsenya sudah sesuai. Kedepannya, Postman akan saya gunakan untuk melakukan API Testing untuk fungsionalitas aplikasi Group Project maupun *software engineering projects* kedepannya.

#### Reflection Publisher-3
