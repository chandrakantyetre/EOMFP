# EOMFP - Product Service

Enterprise Order Management & Fulfillment Platform (EOMFP)

This repository represents a **developer handoff** to the DevOps team.

## Application

Spring Boot REST API for managing products.

## Technology

- Java 21
- Spring Boot
- Maven
- Spring Data JPA
- H2
- Spring Boot Actuator
- JUnit

## Run locally

```bash
mvn clean test
mvn clean package
java -jar target/product-service-0.0.1-SNAPSHOT.jar
```

Application:
http://localhost:8081

Health:
http://localhost:8081/actuator/health

## API examples

Create:

```bash
curl -X POST http://localhost:8081/api/products   -H "Content-Type: application/json"   -d '{"name":"Laptop","category":"Electronics","price":70000,"stockQuantity":10,"active":true}'
```

List:

```bash
curl http://localhost:8081/api/products
```

Get:

```bash
curl http://localhost:8081/api/products/1
```

## DevOps handoff

The developer team has completed the initial application and basic test.

The DevOps team is responsible for taking this code through:

Git/GitHub -> CI -> build/test -> code quality -> Docker -> registry -> Kubernetes -> cloud -> monitoring.

## Important

This is the first service only. Additional EOMFP services will be introduced later.
