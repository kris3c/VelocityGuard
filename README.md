# VelocityGuard

A distributed rate limiting service built with Java, Spring Boot, and Redis.

VelocityGuard helps protect APIs and backend services from abuse by enforcing configurable rate limits across multiple application instances. The service supports multiple rate-limiting strategies and provides a REST API for dynamic configuration and monitoring.

---

## Features

- Distributed rate limiting using Redis
- Multiple algorithms:
  - Token Bucket
  - Sliding Window
  - Fixed Window
  - Leaky Bucket
- Dynamic configuration management
- REST API for rate limit checks
- Redis-backed shared state
- Prometheus metrics support
- Docker deployment support
- Thread-safe implementation
- Horizontal scalability

---

## Tech Stack

- Java 21
- Spring Boot 3
- Redis
- Maven
- Docker
- Prometheus

---

## Architecture

```text
Client
   |
   v
Rate Limiter Service
   |
   v
Redis
```

The service stores rate-limiting state in Redis, allowing multiple application instances to share limits consistently across a distributed environment.

---

## Quick Start

### Prerequisites

- Java 21+
- Redis
- Maven

### Run Locally

```bash
git clone https://github.com/<your-username>/velocityguard.git

cd velocityguard

./mvnw spring-boot:run
```

### Verify Service

```bash
curl http://localhost:8080/actuator/health
```

---

## Example Request

```bash
curl -X POST http://localhost:8080/api/ratelimit/check \
-H "Content-Type: application/json" \
-d '{
  "key":"user:123",
  "tokens":1
}'
```

### Example Response

```json
{
  "allowed": true,
  "remainingTokens": 9
}
```

---

## Supported Algorithms

### Token Bucket

Allows burst traffic while maintaining a steady refill rate.

### Sliding Window

Provides accurate request tracking within a moving time window.

### Fixed Window

Simple and memory-efficient rate limiting strategy.

### Leaky Bucket

Processes requests at a constant rate and smooths traffic spikes.

---

## Monitoring

Application metrics are available through:

```text
/actuator/health
/actuator/metrics
/actuator/prometheus
```

---

## Docker

```bash
docker build -t velocityguard .

docker run -p 8080:8080 velocityguard
```

---

## Future Improvements

- Dashboard for real-time monitoring
- Rate limit analytics
- Multi-region Redis support
- Advanced policy management

---

## License

MIT License
