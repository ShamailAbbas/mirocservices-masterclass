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

Additional files:

- `docker-compose.yml` — Examples and notes for running containers locally (contains helpful docker run examples in comments).
- Root `README.md` — (this file) overview and quickstart.

## Prerequisites

- Docker & Docker Compose (for multi-container local orchestration)
- Node.js (14+ recommended) & npm/yarn (for frontend and Node services)
- Python 3.8+ (for Python-based services)
- Java 11+ & Maven (for `order-management` Spring Boot app)
- Go 1.16+ (for `shipping-and-handling`)

Note: The exact versions used for development may vary per service. Check each service folder for more precise requirements.

## Quick start — bring up everything with Docker Compose

The repository includes a `docker-compose.yml` with useful run examples commented at the top. A simple way to try the full system locally is to build and run the containers with Docker Compose:

1. From the repository root, build and run (foreground):

```bash
docker-compose up --build
```

2. Or start detached:

```bash
docker-compose up --build -d
```

3. Follow logs with:

```bash
docker-compose logs -f
```

Notes:

- The compose file in this repository contains example `docker run` commands and environment variable hints as comments. Use those commented examples as a quick reference for running individual images or customizing environment variables.
- When containers are started using Docker networking, you can address services by their container name (for example, a service named `product-catalog` will be reachable at `http://product-catalog:3000` from other containers, depending on the service port).

## Run services individually (local development)

Each service has its own folder. The following are common commands and environment variables to run the services locally for development. Check the service folder README for service-specific details.

- ecommerce-ui (frontend + small server)

  - Frontend (development):

    ```bash
    cd ecommerce-ui/client
    npm install
    npm start
    ```

    By default the React dev server runs on `http://localhost:3000`.

  - Server (Node backend that the UI can call):
    ```bash
    cd ecommerce-ui/server
    npm install
    node server.js
    ```

- product-catalog (Node)

  ```bash
  cd product-catalog
  npm install
  node index.js
  ```

- product-inventory (Python)

  ```bash
  cd product-inventory
  python3 -m venv venv
  source venv/bin/activate
  pip install -r requirements.txt
  python inventory_api.py
  ```

- order-management (Spring Boot / Java)

  ```bash
  cd order-management
  ./mvnw spring-boot:run
  # or with an installed maven
  mvn spring-boot:run
  ```

- shipping-and-handling (Go)

  ```bash
  cd shipping-and-handling
  go run main.go
  ```

- profile-management (Node)

  ```bash
  cd profile-management
  npm install
  node auth_api.js
  ```

- contact-support-team (Python)
  ```bash
  cd contact-support-team
  python3 -m venv venv
  source venv/bin/activate
  pip install -r requirements.txt
  python server.py
  ```

Environment variables (examples)

Some services require environment variables (these are examples pulled from the repository's docker-compose comments):

- `REACT_APP_*` variables for the frontend to point to API hosts (e.g. `REACT_APP_PRODUCT_API_HOST`, `REACT_APP_INVENTORY_API_HOST`, `REACT_APP_ORDER_API_HOST`, etc.).
- `SPRING_DATA_MONGODB_URI` / `SPRING_DATA_MONGODB_DATABASE` for `order-management` when using MongoDB.
- `MONGO_URI` for services that connect to MongoDB (e.g. `shipping-and-handling` commented example).
- Database connection environment variables for services that use MySQL/Postgres (see the top-of-repo docker run examples).

If you run services with Docker you can pass those variables via `-e` flags or in a `.env` file and reference them from `docker-compose.yml`.

## Useful examples (taken from the repository comments)

Run the UI container:

```bash
docker run --network=my-network -p 4000:4000 \
	-e REACT_APP_PROFILE_API_HOST=http://profile-management \
	-e REACT_APP_PRODUCT_API_HOST=http://product-catalog \
	-e REACT_APP_INVENTORY_API_HOST=http://product-inventory \
	-e REACT_APP_ORDER_API_HOST=http://order-management \
	-e REACT_APP_SHIPPING_API_HOST=http://shipping-and-handling \
	-e REACT_APP_CONTACT_API_HOST=http://contact-support-team \
	--name ecommerce-ui thallavi/ecommerce-ui
```

Run order-management with MongoDB (example):

```bash
docker run --network my-network \
	-e PRODUCT_INVENTORY_API_HOST=http://product-inventory \
	-e PRODUCT_CATALOG_API_HOST=http://product-catalog \
	-e SHIPPING_HANDLING_API_HOST=http://shipping-and-handling \
	-e SPRING_DATA_MONGODB_URI=mongodb://host:27017/name \
	-e SPRING_DATA_MONGODB_DATABASE=order_management \
	-p 9090:9090 --name order-management thallavi/order-management
```

## Debugging and troubleshooting

- Check container logs: `docker-compose logs -f <service-name>` or `docker logs -f <container-name>`.
- If a service fails to start due to missing environment variables, inspect the service README and provide any required DB connection strings or credentials.
- When ports conflict, stop the occupying process or change the port in the service's config/launch command.

## Contributing

Contributions are welcome. Small ways to help:

- Improve service READMEs with setup or troubleshooting steps.
- Add tests and CI configuration for build verification.
- Provide docker images and update `docker-compose.yml` for easier local dev.

When contributing, open a pull request with a clear description of changes and include any steps to reproduce or test.

## License

This repository is provided for educational purposes. No license is specified in this repository. If you plan to publish or reuse code commercially, add a suitable open-source license file (for example `MIT`, `Apache-2.0`, etc.).

## Where to go next

- Inspect each service folder for service-specific instructions and examples:
  - `ecommerce-ui/README.md`
  - `product-catalog/README.md`
  - `product-inventory/README.md`
  - `order-management/README.md`
  - `shipping-and-handling/README.md`
  - `profile-management/README.md`
  - `contact-support-team/README.md`

If you'd like, I can also:

- Add or update individual service README files with run commands and environment variable details.
- Create a simple `docker-compose.override.yml` for local development to expose ports and set sensible defaults.

---
