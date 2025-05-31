# 🏥 Patient Management System

A microservices-based **Patient Management System** built with Spring Boot, Spring Cloud Gateway, Kafka, gRPC, and JWT Authentication. This project demonstrates the integration of REST APIs, service-to-service communication via gRPC, centralized authentication and authorization, and API Gateway routing.

---

## 🚀 Services Overview

### 1. **patient-service**

Handles patient CRUD operations, integrates with `billing-service` via gRPC, and publishes events to Kafka.

#### ✅ Features

* REST endpoints to manage patients
* Email uniqueness check
* gRPC call to `billing-service`
* Publishes `PATIENT_CREATED` event to Kafka
* Validations using `jakarta.validation`

📚 **Endpoints**

- `GET /patients` — Retrieve the list of all patients
- `POST /patients` — Create a new patient
- `PUT /patients/{id}` — Update details of an existing patient
- `DELETE /patients/{id}` — Delete a patient by ID




#### 🔌 gRPC

* Uses `BillingServiceGrpcClient` to call billing microservice on patient creation

#### 📤 Kafka

* Sends `PatientEvent` to topic `patient`

---

### 2. **auth-service**

Provides login and JWT token-based authentication.

#### ✅ Features

* Token generation on successful login
* Token validation endpoint
* Uses `jjwt` for JWT operations
* Passwords are securely encoded

📚 **Endpoints**

- `POST /auth/login` — Generate JWT token
- `GET /auth/validate` — Validate JWT token


#### 🛡️ Security

* Authenticates via email/password
* JWT token includes user role and expiry

---

### 3. **api-gateway**

Routes requests to respective microservices and validates JWT tokens.

#### ✅ Features

* Routes APIs like `/api/patients/**` and `/auth/**`
* Validates token before forwarding requests using `JwtValidationGatewayFilterFactory`
* Supports OpenAPI aggregation

🧭 **Route Examples**

- `/api/patients` → Forwards to `patient-service`
- `/auth/login` → Forwards to `auth-service`
- `/api-docs/patients` → Forwards to `patient-service /v3/api-docs`


#### 📁 Sample Config

```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: patient-service-route
          uri: http://patient-service:4000
          predicates:
            - Path=/api/patients/**
          filters:
            - StripPrefix=1
            - JwtValidation
```

---

## 🧪 Integration Tests

### ✅ AuthIntegrationTest

* Validates login success and failure scenarios using `/auth/login`

### ✅ PatientIntegrationTest

* Uses valid JWT to access `/api/patients`
* Ensures unauthorized access is blocked

---

## 🔧 Technologies Used

* Java 17
* Spring Boot
* Spring Security
* Spring Cloud Gateway
* Kafka
* gRPC
* REST Assured (Testing)
* Docker
* Swagger / OpenAPI 3

---

## 📦 Dockerized Services

Each service includes a Dockerfile. Example:

```dockerfile
FROM openjdk:21-jdk
WORKDIR /app
COPY target/auth-service-0.0.1-SNAPSHOT.jar app.jar
EXPOSE 4005
ENTRYPOINT ["java", "-jar", "app.jar"]
```

---

📚 **Documentation**

- `patient-service` → Swagger available at `/api-docs/patients` (via gateway)
- `auth-service` → Swagger available at `/api-docs/auth` (via gateway)

---
