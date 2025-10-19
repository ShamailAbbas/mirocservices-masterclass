# Microservices Masterclass (mirocservices-masterclass)

A learning repository containing small example microservices that together form a simple e-commerce system. Each service is implemented with a different language/framework so you can explore inter-service communication, containerization, and local development workflows.

This repository is intended as an educational reference and starting point for experimenting with microservice design patterns, local multi-container orchestration, and service-to-service integration.

## Contents

Top-level folders and a short description of each service (see the folder for complete service-specific instructions):

- `ecommerce-ui/` — React frontend and a small Node.js backend used for serving the UI and acting as a gateway in some examples.
- `product-catalog/` — Simple product catalog service (Node.js) that returns product data.
- `product-inventory/` — Inventory API (Python) that exposes stock information.
- `order-management/` — Spring Boot application that demonstrates order processing and persistence.
- `shipping-and-handling/` — Shipping service (Go) used to calculate shipping and costs.
- `profile-management/` — Authentication/profile microservice (Node.js).
- `contact-support-team/` — Contact/support microservice (Python) providing a simple contact API.

## Build, push, run

Quick: build images, push to Docker Hub user `thallavi`, start DBs, run services.

1. Login to Docker Hub:

```bash
docker login
```

2. Build & push images (run from repo root):

   ```bash
   docker build -t thallavi/ecommerce-ui:latest ./ecommerce-ui && docker push thallavi/ecommerce-ui:latest
   docker build -t thallavi/product-catalog:latest ./product-catalog && docker push thallavi/product-catalog:latest
   docker build -t thallavi/product-inventory:latest ./product-inventory && docker push thallavi/product-inventory:latest
   docker build -t thallavi/order-management:latest ./order-management && docker push thallavi/order-management:latest
   docker build -t thallavi/shipping-and-handling:latest ./shipping-and-handling && docker push thallavi/shipping-and-handling:latest
   docker build -t thallavi/profile-management:latest ./profile-management && docker push thallavi/profile-management:latest
   docker build -t thallavi/contact-support-team:latest ./contact-support-team && docker push thallavi/contact-support-team:latest
   ```

3. We can start these services by using docker-compose

   run

   ```bash
   docker-compose up -d
   ```

---

# Note

## Docker Networking if running locally

To enable communication between the Product Catalog microservice and other microservices or applications, ensure that the containers are connected to the same Docker network. You can achieve this by following these steps:

1. Create a Docker network:

   ```bash
   docker network create my-network
   ```

2. Run the microservice like Product Catalog microservice container with the `--network` flag:

   ```bash
   docker run --network my-network -p 3001:3001 product-catalog-microservice
   ```

3. Run other microservice or application containers on the same network:
   ```bash
   docker run --network my-network -p 4000:4000 ecommerce-ui
   ```

By connecting the containers to the same Docker network, they can communicate with each other using their container names as hostnames.
