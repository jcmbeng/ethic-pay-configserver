# 🧩 EthicPay Config Server

> **Centralized configuration microservice** for all EthicPay Spring Cloud microservices.

---

## 📘 Overview

The **EthicPay Config Server** is a Spring Cloud microservice responsible for **centralizing and distributing configuration properties** to all other microservices in the EthicPay ecosystem.

It connects to a **remote Git repository** (`config-repo`) containing YAML configuration files for each service and environment (`dev`, `test`, `prod`).  
At startup, every microservice retrieves its configuration from this server — ensuring consistency, flexibility, and environment-specific setups.

---

## ⚙️ Features

✅ Centralized configuration management  
✅ Supports multiple environments (`dev`, `test`, `prod`)  
✅ Git-based configuration versioning  
✅ Dynamic configuration refresh with Spring Cloud Bus  
✅ Secure configuration using Vault or encrypted properties  
✅ Integration-ready with Eureka, Gateway, Keycloak, Kafka, etc.  
✅ Lightweight and fully containerized for Docker/Kubernetes

---

## 🧱 Architecture Overview

```
                    +----------------------------+
                    |     Git Config Repository  |
                    |  (config-repo.git)         |
                    +-------------+--------------+
                                  |
                                  | HTTPS / SSH
                                  v
                      +--------------------------+
                      |  EthicPay Config Server  |
                      |   (port: 8888)           |
                      +-------------+-------------+
                                  |
              +-------------------+-------------------+
              |                   |                   |
        +-----v-----+       +-----v-----+       +-----v-----+
        | User Svc  |       | Wallet Svc |       | Payment Svc |
        +-----------+       +-----------+       +-------------+
```

---

## 🗂️ Project Structure

```
ethicpay-config-server/
├── src/
│   ├── main/
│   │   ├── java/com/ethicpay/configserver/
│   │   │   └── EthicPayConfigServerApplication.java
│   │   └── resources/
│   │       ├── application.yml
│   │       └── bootstrap.yml (optional)
│   └── test/
│       └── ...
├── pom.xml
└── README.md
```

---

## ⚡ Requirements

| Tool | Version | Description |
|------|----------|-------------|
| **Java** | 17+ | Runtime environment |
| **Spring Boot** | 3.2+ | Application framework |
| **Spring Cloud** | 2023.x | Microservice toolkit |
| **Git** | Latest | Used to fetch configurations |
| **Maven** | 3.9+ | Dependency management |

---

## 🔧 Configuration

### **application.yml**

```yaml
server:
  port: 8888

spring:
  application:
    name: config-server
  cloud:
    config:
      server:
        git:
          uri: https://github.com/ethicpay/config-repo.git
          default-label: main
          clone-on-start: true
          search-paths: .
```

You can switch to an SSH-based connection or a private repository by adding credentials:

```yaml
spring:
  cloud:
    config:
      server:
        git:
          uri: git@github.com:ethicpay/config-repo.git
          private-key: ${GIT_SSH_KEY}
```

---

## 🚀 Running the Config Server

### **1️⃣ Using Maven**

```bash
mvn spring-boot:run
```

### **2️⃣ Using Docker**

```bash
docker build -t ethicpay/config-server .
docker run -d -p 8888:8888 --name config-server ethicpay/config-server
```

### **3️⃣ Test Configuration Endpoint**

```bash
curl http://localhost:8888/wallet-service/dev
```

Expected response:
```json
{
  "name": "wallet-service",
  "profiles": ["dev"],
  "propertySources": [...]
}
```

---

## 🧩 Integration Example (Wallet Service)

Each microservice (e.g., `wallet-service`) must include the following in its `bootstrap.yml`:

```yaml
spring:
  application:
    name: wallet-service
  cloud:
    config:
      uri: http://config-server:8888
      profile: dev
```

---

## 🔐 Security

For production, you should **secure the Config Server** using:
- **HTTPS** (Spring Boot SSL support)
- **Keycloak or OAuth2** for authentication
- **Vault or Secret Manager** for sensitive values
- **Spring Cloud Bus** for event-driven config refreshes

Example secured setup:
```yaml
spring:
  security:
    oauth2:
      resourceserver:
        jwt:
          issuer-uri: https://auth.ethicpay.com/realms/ethicpay
```

---

## 🧠 Best Practices

- ✅ Keep all sensitive credentials in Vault or external secret stores  
- ✅ Separate configurations per environment (`application-dev.yml`, `application-prod.yml`)  
- ✅ Use **Git branches** for major environment separation (e.g., `main`, `staging`, `prod`)  
- ✅ Always test configuration endpoints before deploying new microservices  
- ✅ Enable `/actuator/refresh` to reload configurations without restarting services  

---

## 🛠 Health Check

Once running, verify that the server is healthy:

```bash
curl http://localhost:8888/actuator/health
```

Expected output:
```json
{"status": "UP"}
```

---

## 📦 Docker Compose Example

Here’s a simple service definition for Docker Compose:

```yaml
services:
  config-server:
    image: ethicpay/config-server:latest
    container_name: ethicpay-config-server
    ports:
      - "8888:8888"
    environment:
      - SPRING_PROFILES_ACTIVE=prod
      - GIT_URI=https://github.com/ethicpay/config-repo.git
```

---

## 🧾 License

This microservice is part of the **EthicPay Platform**  
© 2025 — **ETHIC MEDIA SARL**. All rights reserved.

---

## 👨‍💻 Maintainers

**EthicPay Platform – Cloud Engineering Team**  
🌍 [www.ethicpay.com](https://www.ethicpay.com)  
📧 contact@ethicpay.com
