# Voltex: High-Throughput API Gateway & Cache Proxy

A containerized, production-grade backend infrastructure simulating an API gateway, reverse proxy, and distributed caching layer. This project demonstrates high-availability routing, traffic throttling, and multi-tier network isolation using a decoupled microservices architecture.

## Architecture & Network Topology

The infrastructure enforces strict security isolation across two independent Docker networks. The database layer has zero visibility to the public internet, mitigating potential attack surfaces.

```
                  [ External Client Requests ]
                               │
                               ▼
            ┌──────────────────────────────────────┐
            │       Nginx Reverse Proxy            │
            │   (Attached to: frontend-network)    │
            └──────────────────┬───────────────────┘
                               │
            ┌──────────────────┴───────────────────┐
            │        Backend Application           │
            │  (Attached to: frontend & backend)   │
            └──────────────────┬───────────────────┘
                               │
            ┌──────────────────┴───────────────────┐
            │     Redis Cache & Rate Limiter       │
            │   (Attached to: backend-network)     │
            └──────────────────────────────────────┘
```
### Network Design
* **`frontend-network`**: Isolated bridge network for ingress traffic. Bridges the Nginx edge proxy and the application layer.
* **`backend-network`**: Isolated bridge network for internal data transit. Bridges the application layer and the Redis instances. Nginx cannot access this network.

---

## Technical Core & Learning Objectives

### 1. Advanced Docker Networking & Containerization
* **Multi-Tier Networks**: Designing manual bridge networks within Docker to isolate stateful services from edge proxies.
* **Service Discovery**: Leveraging Docker's embedded DNS server for configuration-less communication via service names.
* **Data Persistence**: Configuring automated volume management with Append-Only File (`AOF`) logging for crash-resilient storage state in Redis.

### 2. Nginx Traffic Routing & Load Balancing
* **Reverse Proxying**: Port masking and header forwarding (`X-Real-IP`, `X-Forwarded-For`) to preserve client state.
* **Upstream Round-Robin**: Configuring Nginx `upstream` definitions to distribute loads uniformly across horizontally scaled application instances.
* **Infrastructure Hardening**: Setting buffer thresholds, body size limits, and keep-alive timeouts directly in `nginx.conf`.

### 3. Redis High-Performance State Management
* **Cache-Aside Pattern**: Mitigating database load by offloading expensive or frequent read queries to an in-memory key-value layer with defined Time-To-Live (`TTL`) windows.
* **Distributed Rate Limiting**: Constructing middleware to log, increment, and expire client requests using atomic commands (`INCR`, `EXPIRE`) to prevent race conditions during concurrent high-throughput events.

---

## Technical Stack

* **Reverse Proxy / Load Balancer:** Nginx (Stable Alpine distribution)
* **Container Runtime:** Docker & Docker Compose
* **In-Memory Store / Rate Limiter:** Redis (OSS Edition)
* **Application Layer:** Node.js / Go / Python (Any backend engine of choice executing the proxy rules)

---

## Where This Project Is Useful

* **Microservice Foundations**: Demonstrates how real-world service meshes orchestrate data securely between public ingress and private backends.
* **System Scale & Resilience**: Serves as a sandbox to run load-testing tools (e.g., `k6`, `wrk`) to analyze how upstream proxy timeouts behave under synthetic traffic congestion.
* **Resume/Portfolio Validation**: Provides explicit proof of structural system design capabilities, moving beyond simple application code into modern system architecture and infrastructure-as-code principles.