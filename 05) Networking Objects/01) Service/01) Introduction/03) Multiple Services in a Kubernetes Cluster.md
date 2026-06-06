
---

# 📘 Chapter 14: Multiple Service objects in a Kubernetes cluster

---

> A Kubernetes cluster can contain many Services.

In real-world environments, Kubernetes clusters often contain dozens or even hundreds of Services.

Each Service helps applications communicate reliably inside the cluster.

---

## 1️⃣ One service usually points to one microservice or component

A Service is created to provide stable networking for a specific microservice or group of Pods.

Typically:

```text
One Service
            ↓
One microservice/component
```

The Service acts as a stable network endpoint for its Pods.

---

## 2️⃣ Microservices architecture example

Suppose you have multiple microservices/components in your application:

### Stateless microservices
* Frontend
* Backend
      * User service
      * Payment service
      * Authentication service

### Stateful components
* MySQL database
* Redis cache

Each microservice/component may have its own Service object.

```text
frontend-service --> Frontend pods
backend-service --> Backend pods
user-service --> User service pods
payment-service --> Payment service pods
mysql-service --> Database pods
redis-service --> Redis cache pods
```

All these Services can exist inside the same Kubernetes cluster.

Each Service provides **stable networking, stable DNS, and load balancing** for its own Pods.

> Note: In many **stateful components**, pods may require direct communication with specific pods instead of random service load balancing because Pod identity and roles matter.

### 🌐 One service can point to one or more pods

Example:
* Frontend has only one Pod while backend is having more than one pods.

```text
frontend-service
      ↓
frontend-pod-1



backend-service
      ↓
backend-pod-1
backend-pod-2
backend-pod-3
```

The Service automatically load balances traffic across matching pods.


### 🌐 Can multiple services point to the same pods?

Yes.

Multiple Services can select the same Pods using labels.

This is a common Kubernetes pattern.

Example:

* one Service for internal communication
* another Service for external access

Both Services may point to the same Pods.

### Example

```text
myapp-internal-service
myapp-external-service
          ↓
       Same Pods
```

### 📌 Important Clarification

Services do NOT conflict with each other because each service has its own:

* ClusterIP
* DNS name
* selector
* ports

Example:

| Service          | ClusterIP  |
| ---------------- | ---------- |
| frontend-service | 10.96.0.10 |
| backend-service  | 10.96.0.20 |
| redis-service    | 10.96.0.30 |

Each Service is independently managed by Kubernetes networking.

---

## 3️⃣ Example Kubernetes Cluster Services

```text
Cluster
 ├── frontend-service
 ├── backend-service
 ├── auth-service
 ├── payment-service
 └── redis-service
```
---

## 4️⃣ Inter-Service Communication (Service-to-Service Communication)

Inter-service communication usually means communication between application microservices. Microservices may also communicate with **stateful components** such as databases, Kafka, or Redis.

### Communication Flow between two microservices

> Stateless microservices do not depend on specific Pod identity. Any Pod can handle requests, so they commonly use Service objects for communication.

Example: If the frontend  wants to communicate with backend, it usually connects to a backend-service, which routes traffic to the backend Pods.

> Frontend pods do NOT communicate directly with backend pod IPs instead frontend uses backend-service DNS name

```text
Frontend Pod
      ↓
backend-service
      ↓
Backend Pods
```

### Communication Flow between microservice and stateful component

Microservices may also communicate with stateful components such as databases, Kafka, or Redis through Service objects.

| Communication Type                 | Example            |
| ---------------------------------- | ------------------ |
| Inter-service communication        | frontend → backend |
| Service-to-database communication  | backend → MySQL    |
| Service-to-cache communication     | backend → Redis    |
| Service-to-messaging communication | backend → Kafka    |

```text
backend-service → mysql-service
payment-service → redis-service
```

> For external/client communication stateful also commonly use Service objects.

Example: If the backend wants to communicate with MySQL, it usually connects to a mysql-service, which routes traffic to the MySQL Pods.

> Backend pods do NOT communicate directly with MySQL pod IPs instead backend uses mysql-service DNS name

```text
backend Pods
      ↓
mysql-service
      ↓
mysql Pods
```

### Communication Flow between two stateful component

Example: If communication needs to happen between MySQL and Redis cache

Since stateful infrastructure components usually have specialized responsibilities, they typically do not communicate directly for business workflows. Instead, microservices commonly act as the middle layer.

```text
backend-service
    ↓
MySQL

backend-service
    ↓
Redis
```

### How Kubernetes 'Service' object help in inter-service communication

For example, frontend application does NOT need to know:

* backend Pod IPs
* how many backend Pods exist
* whether backend Pods restarted
* frontend service just need to know about backend-service DNS name. And rest other will be handled by 'Service' object i.e.  **stable networking, service discovery and load balancing**.

---

## 5️⃣ Inter-Pod Communication (Pod-to-Pod Communication)

In many cases, pods of the same microservice need to communicate with each other.

### Communication Flow between pods of stateless microservices.

Example: One backend pod communicating with another backend pod.

```text
backend-pod-1
backend-pod-2
backend-pod-3
backend-pod-4
backend-pod-5
```

> Backend pods typically do not communicate using direct pod IPs. Instead, they commonly use the backend-service DNS name.

```text
backend-pod-1
      ↓
backend-service
      ↓
backend-pod-2
```

### Communication Flow between pods of stateful component.

Some component need direct Pod identity.
Examples:
* databases
* Kafka
* Elasticsearch
* distributed databases and messaging systems

```text
mysql-1 → mysql-0
kafka-2 → kafka-0
```

This internal replication/coordination often requires - 'direct Pod identity' and 'stable Pod DNS'. This is where **StatefulSet and Headless Service** become important.

```text
mysql-1.mysql-headless-service
        ↓
mysql-0.mysql-headless-service
```
---

## 🔑 One-Line Interview Answer

> A Kubernetes cluster can contain multiple Services, where each Service provides stable networking, service discovery, and load balancing for a specific microservice or group of Pods.
