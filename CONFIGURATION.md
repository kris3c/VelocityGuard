# Configuration Guide

## Overview

This document explains how application settings and rate-limiting policies can be configured.

The system supports flexible configuration through application properties and runtime updates, allowing different limits to be applied for various use cases without modifying application code.

---

## Configuration Hierarchy

When processing a request, configuration is resolved using the following priority order:

1. Exact key configuration
2. Pattern-based configuration
3. Default configuration

More specific configurations always take precedence over generic rules.

---

## Default Configuration

Default settings are applied when no key-specific or pattern-based configuration exists.

Example:

```properties
ratelimiter.capacity=10
ratelimiter.refillRate=2
ratelimiter.algorithm=TOKEN_BUCKET
```

These values act as fallback settings for all requests.

---

## Key-Based Configuration

Specific limits can be assigned to individual keys.

Example:

```properties
ratelimiter.keys.premium_user.capacity=50
ratelimiter.keys.premium_user.refillRate=10
```

This configuration applies only to the specified key.

---

## Pattern-Based Configuration

Patterns allow groups of keys to share the same rate-limiting policy.

Example:

```properties
ratelimiter.patterns.user:*.capacity=20
ratelimiter.patterns.user:*.refillRate=5

ratelimiter.patterns.api:*.capacity=100
ratelimiter.patterns.api:*.refillRate=50
```

---

## Supported Patterns

Examples:

```text
user:*        -> user:123, user:abc
api:v1:*      -> api:v1:users, api:v1:orders
*:admin       -> system:admin, user:admin
*             -> matches all keys
```

---

## Runtime Configuration

The application supports updating configuration without modifying source code.

### View Configuration

```bash
curl http://localhost:8080/api/ratelimit/config
```

### Update Default Limits

```bash
curl -X POST http://localhost:8080/api/ratelimit/config/default \
-H "Content-Type: application/json" \
-d '{
  "capacity": 20,
  "refillRate": 5
}'
```

### Configure Specific Keys

```bash
curl -X POST http://localhost:8080/api/ratelimit/config/keys/premium_user \
-H "Content-Type: application/json" \
-d '{
  "capacity": 100,
  "refillRate": 20
}'
```

### Configure Patterns

```bash
curl -X POST http://localhost:8080/api/ratelimit/config/patterns/api:* \
-H "Content-Type: application/json" \
-d '{
  "capacity": 200,
  "refillRate": 50
}'
```

---

## Removing Configuration

### Remove Key Configuration

```bash
curl -X DELETE \
http://localhost:8080/api/ratelimit/config/keys/premium_user
```

### Remove Pattern Configuration

```bash
curl -X DELETE \
http://localhost:8080/api/ratelimit/config/patterns/api:*
```

---

## Reload Configuration

To reload configuration and clear cached state:

```bash
curl -X POST \
http://localhost:8080/api/ratelimit/config/reload
```

This is useful after applying significant configuration changes.

---

## Supported Algorithms

The service supports multiple rate-limiting strategies.

### Token Bucket

Recommended for general-purpose APIs.

Characteristics:

- Allows burst traffic
- Smooth refill behavior
- Balanced performance

### Sliding Window

Recommended when accurate request tracking is required.

Characteristics:

- Precise enforcement
- Consistent rate control
- Predictable behavior

### Fixed Window

Recommended for high-scale workloads.

Characteristics:

- Lower memory usage
- Simple implementation
- Efficient request processing

### Leaky Bucket

Recommended for traffic shaping.

Characteristics:

- Consistent output rate
- Smooth request flow
- Burst absorption

---

## Example Configurations

### General API

```properties
ratelimiter.patterns.api:*.algorithm=TOKEN_BUCKET
ratelimiter.patterns.api:*.capacity=100
ratelimiter.patterns.api:*.refillRate=20
```

### High Volume Services

```properties
ratelimiter.patterns.service:*.algorithm=FIXED_WINDOW
ratelimiter.patterns.service:*.capacity=1000
```

### Critical Endpoints

```properties
ratelimiter.patterns.critical:*.algorithm=SLIDING_WINDOW
ratelimiter.patterns.critical:*.capacity=50
```

### Traffic Control

```properties
ratelimiter.patterns.gateway:*.algorithm=LEAKY_BUCKET
ratelimiter.patterns.gateway:*.capacity=100
ratelimiter.patterns.gateway:*.refillRate=20
```

---

## Configuration Best Practices

- Start with conservative limits
- Monitor usage patterns before increasing capacity
- Use pattern-based rules for consistency
- Reserve key-specific rules for special cases
- Reload configuration after major updates
- Regularly review rate-limiting policies

---

## Monitoring Configuration

Useful endpoints:

```text
/api/ratelimit/config
/api/ratelimit/config/stats
/actuator/health
/actuator/metrics
```

These endpoints help verify active configuration and system status.

---

## Conclusion

A well-structured configuration strategy makes it easier to manage rate-limiting policies across different services and user groups. By combining default, pattern-based, and key-specific configurations, the application can adapt to a wide range of traffic management requirements.
