# API Examples

This directory contains example integrations demonstrating how to interact with the VelocityGuard API from different programming languages and environments.

---

## Overview

The examples included in this directory show common usage patterns such as:

- Performing rate limit checks
- Handling blocked requests
- Processing API responses
- Integrating rate limiting into applications

---

## Available Examples

### Client Implementations

- Java / Spring Boot
- Python
- Node.js
- Go

### Testing Examples

- cURL requests
- API validation examples
- Local development testing

---

## Basic Request Flow

Most integrations follow the same workflow:

1. Send a request to the rate limit endpoint
2. Evaluate the response
3. Continue processing if allowed
4. Apply retry logic when limits are exceeded

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

---

## Example Response

```json
{
  "allowed": true,
  "remainingTokens": 9
}
```

---

## Common Status Codes

| Status Code | Meaning |
|------------|----------|
| 200 | Request allowed |
| 429 | Rate limit exceeded |
| 400 | Invalid request |
| 500 | Internal server error |

---

## Integration Recommendations

### Retry Strategy

When a request is rejected due to rate limiting:

- Respect retry intervals
- Use exponential backoff
- Avoid aggressive retries

### Monitoring

Track:

- Request volume
- Blocked requests
- Response times
- Error rates

---

## Local Testing

Verify the service is running:

```bash
curl http://localhost:8080/actuator/health
```

Expected response:

```json
{
  "status":"UP"
}
```

---

## Additional Resources

- README.md
- CONFIGURATION.md
- PERFORMANCE.md
- LOAD-TESTING.md

---

## Notes

The examples provided are intended as reference implementations and can be adapted to match the requirements of individual applications and deployment environments.
