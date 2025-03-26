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
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Subscriber model struct.`
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Subscriber repository.`
    -   [ ] Commit: `Implement list_all function in Subscriber repository.`
    -   [ ] Commit: `Implement delete function in Subscriber repository.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
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

1. **Do we need an interface/trait for Subscriber, or is a single model struct enough?**  
   In the classic Observer pattern, the Observer is defined as an interface so that different concrete observers can implement custom notification behavior. In BambangShop’s case, our “subscriber” is merely a record containing an ID and callback URL there is no polymorphic behavior or multiple subscriber types requiring different implementations. Therefore a single Rust struct is sufficient; we don’t gain extra flexibility by defining a trait here.


2. **Is using a Vec sufficient for storing subscribers, or is a DashMap (hash map) necessary?**  
   Because each subscriber’s ID (and URL) must be unique, a map keyed by ID naturally enforces uniqueness and provides O(1) lookup, addition, and removal. A Vec would require linear searches to detect duplicates or delete entries, which grows inefficient as the subscriber list scales. Thus DashMap (or any map) is more appropriate for both correctness and performance.


3. **Do we still need DashMap for thread safety, or could we implement a Singleton instead?**  
   A Singleton only ensures a single instance of a repository, not thread-safe concurrent access. Since HTTP handlers run concurrently and may mutate the subscriber list simultaneously, thread safety is essential. DashMap provides built‑in concurrent locking per bucket, so it’s both simpler and safer than building a custom synchronization wrapper around a Singleton.

   
#### Reflection Publisher-2

1. **Why separate Service and Repository from Model?**  
   Following the Single Responsibility Principle, the Repository layer is responsible solely for data persistence (CRUD operations), while the Service layer encapsulates business logic and orchestrates interactions between repositories and other components (e.g., validation, notification triggering). Keeping these concerns separate improves maintainability, testability, and allows swapping or mocking layers independently during testing.


2. **What if we only use the Model? How would interactions affect code complexity?**  
   If Models handled persistence, validation, and business rules all in one, each struct (Program, Subscriber, Notification) would become bloated with mixed responsibilities. For example, subscribing a user would require the Subscriber model to both validate input, update storage, and manage notification logic. As the application grows, tangled code paths and duplicated logic across models would drastically increase complexity and make it difficult to isolate bugs or add new features.


3. **How Postman helps testing and which features are useful?**  
   Postman streamlines API testing by allowing me to construct and send HTTP requests without writing client code. Key features I find valuable include:
    - **Collections & Environments:** Organize related requests and switch between configurations (e.g., development vs production).
    - **Pre-request scripts & Tests:** Automate setup (auth tokens) and assert response correctness.
    - **Variables & Parameterization:** Easily reuse dynamic values across requests.
    - **Documentation Generation:** Share clear API docs with teammates.

   These capabilities accelerate iterative development and collaboration for our group project and any future web services.

#### Reflection Publisher-3
